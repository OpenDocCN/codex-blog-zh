# 自动扩展 Fargate 服务的云形成示例

> 原文：<https://medium.com/codex/cloudformation-example-for-auto-scaling-fargate-service-c660dfdb840a?source=collection_archive---------7----------------------->

2021 年 5 月 5 日

作者托马斯

![](img/ede7fad11d505368fd3c665dbdbe7c0e.png)

[疾控中心](https://unsplash.com/@cdc?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/microscopic?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片

从代码和基础设施的角度来看，现代 web 应用程序都需要良好的可伸缩性。虽然我认为 Lambda 函数是构建可伸缩性的一个很好的平台，但 Fargate 也是一个有效的选择。大多数开发人员都熟悉容器，Fargate 为我们提供了一个很好的起点，让我们可以在云中运行这些容器，而不会妨碍开发人员编写应用程序。我提到这一点是为了说明我理解用容器开发比开发 Lambda 函数更有吸引力，因为 Lambda 函数不容易在本地运行。容器在云中的功能与它们在本地机器上的功能相同。

容器和 Fargate 可能会提供优秀的开发人员体验，但是有一样东西 Fargate 不能提供，Lambda 函数可以；自动缩放。当我将代码部署到 Lambda 函数时，我就完成了。AWS 为我处理供应和扩展以及其他操作方面。当我将一个容器部署到一个 Fargate 服务时，我仍然必须处理它的伸缩方式，因为 AWS 不会为我开箱即用。我过去曾经写过一个简单的 Fargate 服务 CloudFormation 模板，但是这个模板不包括自动缩放。我想回去，并添加自动缩放，给一个更好的基础。

我创建的模板基于最初的简单 Fargate 服务模板。必要的添加基于自动扩展资源和触发扩展的 CloudWatch 警报。自动扩展资源是一个 IAM 角色，一个应用程序自动扩展`ScalableTarget`，一个应用程序自动扩展`ScalingPolicy`来扩展服务，另一个应用程序自动扩展`ScalingPolicy`来缩减服务。自动扩展目标旨在将自动扩展策略指向正确的服务，并且扩展策略旨在由外部事件触发。在我的模板中，外部事件是一个 CloudWatch 警报。

CloudWatch 警报可以跟踪 AWS(内置或用户定义的)中的指标，并基于这些指标触发操作。对于我的模板，我选择根据负载均衡器在一分钟内收到的请求数量进行伸缩。如果请求的数量超过了我预先定义的限制，Fargate 服务就会通过添加一个新任务来帮助处理这个负载。当请求的数量下降到我预先定义的限制之下时，Fargate 服务也相应减少。通过这种方式，我们可以在服务背后动态地分配适当数量的计算资源。

我选择在我的模板中使用每分钟的请求数，但整个目的并不是说基于请求的伸缩是最好的想法。我只是想给出一个基线或一个起点，我希望有人可以利用这个模板，并调整它，以最适合他们的需求。基于请求的扩展策略也让我更容易测试一切是否正常。要验证我的服务是否可以扩展，我需要做的就是向它发送一些请求，并监控 Fargate 为我的服务完成的任务数量。随着我发送更多的请求，我可以监视警报的仪表板，并观察自动缩放事件作为另一种形式的验证被调度。

我将添加自动缩放资源到这篇文章，但不是整个功能模板。要看完整模板，可以参考[我的 AWS-cloud formation-参考 GitHub repo](https://github.com/thomasstep/aws-cloudformation-reference/blob/master/fargate/auto-scaling/auto-scaling-service.yml) 。

```
AutoScalingRole:
    Type: AWS::IAM::Role
    Properties:
      AssumeRolePolicyDocument:
        Version: '2012-10-17'
        Statement:
          - Effect: Allow
            Principal:
              Service:
                - ecs-tasks.amazonaws.com
            Action:
              - 'sts:AssumeRole'
      Path: '/'
      Policies:
        - PolicyName: root
          PolicyDocument:
            Version: '2012-10-17'
            Statement:
              - Effect: Allow
                Action:
                  - ecs:DescribeServices
                  - ecs:UpdateService
                  - cloudwatch:DeleteAlarms
                  - cloudwatch:DescribeAlarms
                  - cloudwatch:PutMetricAlarm
                Resource: '*'

  AutoScalingTarget:
    Type: AWS::ApplicationAutoScaling::ScalableTarget
    Properties:
      MinCapacity: 1
      MaxCapacity: !Ref MaxContainers
      ResourceId: !Join
        - '/'
        - - service
          - !Ref EcsCluster
          - !GetAtt FargateService.Name
      ScalableDimension: ecs:service:DesiredCount
      ServiceNamespace: ecs
      RoleARN: !GetAtt AutoScalingRole.Arn

  ScaleUpPolicy:
    Type: AWS::ApplicationAutoScaling::ScalingPolicy
    Properties:
      PolicyName: !Sub '${FargateService}ScaleUpPolicy'
      PolicyType: StepScaling
      ScalingTargetId: !Ref AutoScalingTarget
      StepScalingPolicyConfiguration:
        AdjustmentType: ChangeInCapacity
        Cooldown: 60
        MetricAggregationType: Average
        StepAdjustments:
          - MetricIntervalLowerBound: 0
            ScalingAdjustment: 1
  ScaleDownPolicy:
    Type: AWS::ApplicationAutoScaling::ScalingPolicy
    Properties:
      PolicyName: !Sub '${FargateService}ScaleDownPolicy'
      PolicyType: StepScaling
      ScalingTargetId: !Ref AutoScalingTarget
      StepScalingPolicyConfiguration:
        AdjustmentType: ChangeInCapacity
        Cooldown: 60
        MetricAggregationType: Average
        StepAdjustments:
          - MetricIntervalUpperBound: 0
            ScalingAdjustment: -1

  AlarmHighRequests:
    Type: AWS::CloudWatch::Alarm
    Properties:
      ActionsEnabled: TRUE
      AlarmActions:
        - !Ref ScaleUpPolicy
      AlarmDescription: !Sub
        - 'Scale Up Alarm based on requests for ${FargateServiceName}'
        - FargateServiceName: !GetAtt FargateService.Name
      ComparisonOperator: GreaterThanThreshold
      DatapointsToAlarm: 2
      # the dimensions can be found in the console after selecting a namespace to filter by
      Dimensions:
        - Name: TargetGroup
          Value: !GetAtt TargetGroup.TargetGroupFullName
      EvaluationPeriods: 3
      # the metric name can be found in the console on the screen before a metric is graphed
      MetricName: RequestCountPerTarget
      # the namespace can be found in the console on the first screen before filtering metrics
      Namespace: AWS/ApplicationELB
      OKActions:
        - !Ref ScaleDownPolicy
      Period: 60
      Statistic: Sum
      Threshold: 3000
      TreatMissingData: ignore
      Unit: None # comes from the metric
```

*原载于 2021 年 5 月 5 日*[*【https://thomasstep.com】*](https://thomasstep.com/blog/cloudformation-example-for-auto-scaling-fargate-service)*。*
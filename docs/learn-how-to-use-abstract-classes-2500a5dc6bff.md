# 学习如何使用抽象类

> 原文：<https://medium.com/codex/learn-how-to-use-abstract-classes-2500a5dc6bff?source=collection_archive---------18----------------------->

![](img/5aaea3332d158b6bcea673c864f2809a.png)

阿诺·弗朗西斯卡在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 问题陈述

考虑一个具有以下约束的场景…
1。中间系统从源系统接收一个非常大的请求，将请求分成较小的块，验证它，然后将其发送到不同的目标系统。
2。请求有时包含特定的数据，有时不包含。
3。请求中的数据量可能会随着时间的推移而增加。

# 示例问题

假设一家 XYZ 公司有一个系统，该系统接收雇员的一般信息，如姓名、部门和电子邮件，以及雇员的银行帐户信息，以处理他/她的工资。XYZ 公司很少有员工不通过数字平台领取工资。在这种情况下，银行信息将为空。该公司使用一个系统组织员工的一般信息，另一个系统以数字方式处理工资。

```
{
   "employee-id":"A104",
   "personal-information":{
      "name":"Mate Jones",
      "email":"[mateJanos@mail.com](mailto:makeJanos@gmail.com)",
      "phone-no":"+9230112345678"
   },
   "bank-information":{
      "account-no":"12345678",
      "bank-name":"ABC Bank",
      "branch-name":"ABC Branch",
      "branch-code":"1234"
   }
}
```

# *如何实现？*

## 1.天真的方法

创建一个将接收整个请求的函数，并检查一个特定的字段，即银行信息。如果存在，则将其发送到特定的目标系统。

这种方法的问题是有很多假设。随着数据的增加，if-else 的数量也会增加。很难做出任何改变，找到一个代码块来处理特定数据并将其发送到某个目标系统。更改验证，即以前处理个人信息只需要电子邮件，现在电话号码也是强制性的。

因此，要进行这些更改，首先我们必须找到处理个人信息的 if 块，然后添加一个新的 if 块来检查电话号码的存在。

## 2.解决办法

首先，我们要明白自己在做什么？
a .检查请求中是否存在数据。
b .验证数据。
c .将数据发送到目标系统。

在上述步骤中， **a** 和 **b** 是数据特定的。就像每个数据都有检查其存在的方法一样，对于个人数据，我们需要调用个人数据字段的 getter，并且每个数据都有自己的验证规则。但是步骤 **c** 对于所有请求都是通用的。

让我们看看 step **c** 是如何对所有请求通用的，以及抽象类如何帮助我们编写可读、可重用和易于扩展的代码。

我们将设计一个抽象类来保存通用的功能，并根据它们的目标系统和数据为子类定义一些实现的方法。

首先，创建一个抽象类 RequestExecutor <t>，如下所示。*其中* ***T*** *是孩子想要处理的数据类型。*</t>

```
public abstract class RequestExecutor<T> {

    public void execute(EmployeeRequest employeeRequest) {
    }

    public void sendRequest(T data) {

    }
    public abstract Boolean checkRequestExists(EmployeeRequest employeeRequest);

   public abstract void validateData(EmployeeRequest employeeRequest); public abstract T extractData(EmployeeRequest employeeRequest); public abstract String getURI();
}
```

1.  execute():这个函数将执行所有需要的设置。从请求中提取数据、执行验证和发送请求。由于这些设置对于请求中的每个数据都是通用的，所以在具体的类中定义逻辑是有意义的。
2.  sendRequest():这个函数将向目标系统发送请求。
3.  checkRequestExists():这个函数只检查请求是否包含数据。
4.  validateData():它将对数据执行验证，如果数据无效，它将抛出 InvalidDataException。
5.  extractData():它将从请求中提取数据。它就像一个映射函数。
6.  getURI():这个函数的目的是返回目标系统的 URL。每个数据都有不同的目标系统，因此每个子节点都将返回其 URL。

我们将实现前两个函数，然后编写两个客户端/子类来提供由父类定义的功能。

```
public void execute(EmployeeRequest employeeRequest) {
    if (checkRequestExists(employeeRequest)) {
        this.validateData(employeeRequest);
        T data = this.extractData(employeeRequest);
        this.sendRequest(data);
    }
}public void sendRequest(T data) {
    String URL = this.getURI();
    System.*out*.printf("Sending Data %s to %s%n", data.toString(), URL);
}
```

看看我们的代码现在变得多干净。我们已经成功地编写了一个可以反复使用的代码。现在每个子类都将使用这个代码来处理请求。

# 个人信息处理客户端

```
public class PersonalInformationExecutor extends RequestExecutor<PersonalInformationDto> {

    @Override
    public Boolean checkRequestExists(EmployeeRequest employeeRequest) {
        return employeeRequest.getEmployee()!=null;
    }

    @Override
    public void validateData(EmployeeRequest employeeRequest) {
        if(StringUtil.*isNullOrEmpty*(employeeRequest.getEmployeeId()))
            throw new InvalidDataException("Employee ID is Empty");

        if(StringUtil.*isNullOrEmpty*(employeeRequest.getEmployee().getEmail()))
            throw new InvalidDataException("Employee email is Empty");

    }

    @Override
    public PersonalInformationDto extractData(EmployeeRequest employeeRequest) {
        PersonalInformationDto dto = new PersonalInformationDto();
        dto.setEmployee(employeeRequest.getEmployee());
        dto.setEmployeeId(employeeRequest.getEmployeeId());
        return dto;
    }

    @Override
    public String getURI() {
        return "https://www.personalinformation.com/api/personalInformation";
    }
}
```

该客户端覆盖所有其余的四个方法，并提供其实现。让我们一个一个地看。

1.  checkRequestExists()
    因为该请求与员工个人信息相关。因此，它将检查员工的个人信息是否存在。
2.  目前，我们有两条规则。只有当员工的请求包含员工 id 和员工的电子邮件地址时，才会处理员工的个人信息。
3.  extractData()
    它会根据目标系统的需求从请求中提取数据。
4.  getURI()
    返回目标系统的 URI。

# 银行信息处理客户端

```
public class BankInformationExecutor extends RequestExecutor<BankInformationDto> {
    @Override
    public Boolean checkRequestExists(EmployeeRequest employeeRequest) {
        return employeeRequest.getBankInformation()!=null;
    }

    @Override
    public void validateData(EmployeeRequest employeeRequest) throws InvalidDataException {
        if(StringUtil.*isNullOrEmpty*(employeeRequest.getEmployeeId()))
            throw new InvalidDataException("Employee Id is empty");
        if(StringUtil.*isNullOrEmpty*(employeeRequest.getBankInformation().getAccountNo()))
            throw new InvalidDataException("Account No is required");

        if(StringUtil.*isNullOrEmpty*(employeeRequest.getBankInformation().getBranchCode()))
            throw new InvalidDataException("Branch Code is required");

        if(StringUtil.*isNullOrEmpty*(employeeRequest.getBankInformation().getBranchName()))
            throw new InvalidDataException("Branch name is required");
    }

    @Override
    public BankInformationDto extractData(EmployeeRequest employeeRequest) {
        BankInformationDto bankInformationDto = new BankInformationDto();
        bankInformationDto.setBankInformation(employeeRequest.getBankInformation());
        bankInformationDto.setEmployeeId(employeeRequest.getEmployeeId());
        return bankInformationDto;
    }

    @Override
    public String getURI() {
        return "https://www.digitalBank.com/employee";
    }
}
```

这里我就说说 validateRequest()方法。在前一个客户端中，我们有 2 个规则来验证请求，而在这个客户端中，我们有 3 个规则来验证请求。而且添加新规则超级容易。

看看我们的代码是如何变得清晰、可扩展而不影响其他代码，并且可读的。

驱动程序类来测试上面的代码。

```
public class AbstractClassDemoMain {

    public static void main(String[] args) {
        EmployeeRequest employeeRequest = new EmployeeRequest();
        employeeRequest.setEmployeeId("A104");

        Employee employee = new Employee();
        employee.setEmail("mateJanos@mail.com");
        employee.setName("Mate Jones");
        employee.setPhoneNo("+92301123456789");

        employeeRequest.setEmployee(employee);

        BankInformation bankInformation = new BankInformation();
        bankInformation.setBankName("ABC Bank");
        bankInformation.setAccountNo("12345678");
        bankInformation.setBranchName("ABC Branch");
        bankInformation.setBranchCode("1234");

        employeeRequest.setBankInformation(bankInformation);

        List<RequestExecutor<?>> executorsList = new ArrayList<>();

        executorsList.add(new BankInformationExecutor());
        executorsList.add(new PersonalInformationExecutor());

        for (RequestExecutor<?> requestExecutor :
                executorsList) {
            requestExecutor.execute(employeeRequest);
        }
    }
}
```

> 上面的驱动程序类将产生下面的输出。

> — —处理请求—
> — —检查银行请求是否存在—
> — —验证银行请求—
> — —银行请求有效—
> — —提取银行请求—
> — —将银行信息数据发送至{employeeId='A104 '，Bank information = Bank information { account no = ' 12345678 '，bankName='ABC Bank '，branchName='ABC Branch '，branchCode='1234'}}至[https://www.digitalBank.com/employee【T20](https://www.digitalBank.com/employee)
> 
> — —处理请求—
> — —检查个人信息请求是否存在—
> — —验证个人信息请求—
> — —银行请求有效—
> — —提取个人信息请求—
> — —发送数据个人信息至{employeeId='A104 '，Employee = Employee { name = ' Mate Jones '，[email='mateJanos@mail.com【T6 ' '，电话号码='+92301123456789'}}至](mailto:email='mateJanos@mail.com)[https://www.personalinformation.com/api/personalInformation](https://www.personalinformation.com/api/personalInformation)

现在，让我们从员工请求中删除银行请求。

```
import model.BankInformation;
import model.Employee;
import model.EmployeeRequest;
import service.RequestExecutor;
import service.impl.BankInformationExecutor;
import service.impl.PersonalInformationExecutor;

import java.util.ArrayList;
import java.util.List;

public class AbstractClassDemoMain {

    public static void main(String[] args) {
        EmployeeRequest employeeRequest = new EmployeeRequest();
        employeeRequest.setEmployeeId("A104");

        Employee employee = new Employee();
        employee.setEmail("mateJanos@mail.com");
        employee.setName("Mate Jones");
        employee.setPhoneNo("+92301123456789");

        employeeRequest.setEmployee(employee);

        BankInformation bankInformation = new BankInformation();
        bankInformation.setBankName("ABC Bank");
        bankInformation.setAccountNo("12345678");
        bankInformation.setBranchName("ABC Branch");
        bankInformation.setBranchCode("1234");

//        employeeRequest.setBankInformation(bankInformation);

        List<RequestExecutor<?>> executorsList = new ArrayList<>();

        executorsList.add(new BankInformationExecutor());
        executorsList.add(new PersonalInformationExecutor());

        for (RequestExecutor<?> requestExecutor :
                executorsList) {
            requestExecutor.execute(employeeRequest);
        }
    }
}
```

> 没有银行信息的请求日志。

> — —处理请求—
> —检查银行请求是否存在—
> —处理请求—
> —检查个人信息请求是否存在—
> —验证个人信息请求—
> —银行请求有效—
> —提取个人信息请求—
> —发送个人信息数据至{employeeId='A104 '，Employee = Employee { name = ' Mate Jones '，[email='mateJanos@mail.com](mailto:email='mateJanos@mail.com)'，phoneNo='+

执行者只检查银行请求的存在，但从不执行任何其他操作。

让我们从个人信息中删除必填字段，看看会发生什么？

```
public class AbstractClassDemoMain {

    public static void main(String[] args) {
        EmployeeRequest employeeRequest = new EmployeeRequest();
        employeeRequest.setEmployeeId("A104");

        Employee employee = new Employee();
//        employee.setEmail("mateJanos@mail.com");
        employee.setName("Mate Jones");
        employee.setPhoneNo("+92301123456789");

        employeeRequest.setEmployee(employee);

        BankInformation bankInformation = new BankInformation();
        bankInformation.setBankName("ABC Bank");
        bankInformation.setAccountNo("12345678");
        bankInformation.setBranchName("ABC Branch");
        bankInformation.setBranchCode("1234");

        employeeRequest.setBankInformation(bankInformation);

        List<RequestExecutor<?>> executorsList = new ArrayList<>();

        executorsList.add(new BankInformationExecutor());
        executorsList.add(new PersonalInformationExecutor());

        for (RequestExecutor<?> requestExecutor :
                executorsList) {
            try{
                requestExecutor.execute(employeeRequest);

            }catch(InvalidDataException ex){
                System.*out*.println("Error:" + ex.getMessage());
            }
        }
    }
}
```

> 包含无效数据的请求日志。

> — —处理请求—
> — —检查银行请求是否存在—
> — —验证银行请求—
> — —银行请求有效—
> — —提取银行请求—
> — —将银行信息数据发送至{employeeId='A104 '，Bank information = Bank information { account no = ' 12345678 '，bankName='ABC Bank '，branchName='ABC Branch '，branchCode='1234'}}至[https://www.digitalBank.com/employee](https://www.digitalBank.com/employee)
> 
> ———处理请求——
> ———检查个人信息请求是否存在——
> ———验证个人信息请求— —
> 错误:员工邮箱为空

在这种情况下，程序将银行信息发送到各自的目标系统，由于验证错误，没有处理员工的个人信息。

# 结论

1.  一个地方的通用功能。
2.  代码很容易扩展。例如，如果我们想将银行和个人信息发送到一个学习平台，以便为一个员工创建一个学习帐户，我们可以通过添加一个新的客户端来实现这一点。
3.  定义数据规则的更好、更简单的方法。
4.  如果任何一个客户端想要发送一个请求或者按照他们的方式进行处理，他们仍然可以覆盖这些公共函数，而不会影响任何其他客户端的功能。

Github 链接代码:[https://github.com/MuhammadHassnain/abstractclasseddemo](https://github.com/MuhammadHassnain/abstractclasseddemo)

代码包含每个步骤的提交。如果你喜欢这篇文章，鼓掌将是完美的，并鼓励我写更多。:)
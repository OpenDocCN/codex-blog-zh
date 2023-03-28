# 使用 TypeScript 同步 Django REST 框架和 Vue.js:第 2 部分

> 原文：<https://medium.com/codex/use-typescript-to-synchronize-django-rest-framework-and-vue-js-a2af07aabf89?source=collection_archive---------12----------------------->

![](img/8f17eb64c8a94931b7a9b370979a106d.png)

由[史蒂夫·哈拉马](https://unsplash.com/@steve3p_0?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 第 2 部分:消费元数据

请参见 [*第一部分:生成 API 元数据*](/@johnfraney/use-typescript-to-synchronize-django-rest-framework-and-vue-js-d103cf416e23) 以跟上进度。这篇文章使用了通过文章中描述的步骤生成的`User.json`元数据。

## (非)结构化数据

在客户端代码中使用 API 元数据的想法并不是革命性的。 [AutoRest](https://github.com/Azure/AutoRest) 使用 [OpenAPI](https://swagger.io/resources/open-api/) 模式为多种语言生成代码，Django REST 框架的文档建议使用元数据来启动客户端库:

> API 模式是一个有用的工具，它允许一系列用例，包括生成参考文档，或者驱动可以与 API 交互的动态客户端库。
> -[https://www.django-rest-framework.org/api-guide/schemas/](https://www.django-rest-framework.org/api-guide/schemas/#generating-an-openapi-schema)

本系列文章的第一部分介绍了如何生成在 JavaScript/TypeScript 客户机中使用的元数据，而本文兑现了这一承诺，并在 [Vue](https://vuejs.org/) 前端中使用用户元数据。

怎么会？有很多方法可以使用 API 元数据来驱动前端，比如:

*   生成常数
*   创建类型安全的窗体
*   自动表单生成
*   数据类生成

这篇博文将涉及类型安全和(大部分)自动生成的表单。

## 来自 JSON 元数据的类型安全表单

下面是一个简单的 Vue 组件，包含一个创建新用户的表单:

```
<template>
  <div id="app">
    <h1>User Form</h1>
    <form [@submit](http://twitter.com/submit).prevent="onSubmit">
      <label>First name</label>
      <input
        v-model="form.firstName"
      > <label>Last name</label>
      <input
        v-model="form.lastName"
      > <label>Email address</label>
      <input
        v-model="form.firstName"
      > <label>Username</label>
      <input
        v-model="form.username"
      > <button type="submit">Submit</button>
    </form>
  </div>
</template>
<script lang="ts">
import Vue from 'vue';export default Vue.extend({
  name: 'App', data() {
    return {
      form: {},
    }
  },

  methods: {
    onSubmit() {
      // Pretend a user is created
    },
  },
});
</script>
```

注意到表格有什么问题吗？

去吧，好好看看。

首先，它看起来像一个标准的形式，当然。然而，你看得越多，你就越会意识到它是多么的简单。整体和局部都不起眼。它是如此之少，以至于很可能被认为是无可非议的。毕竟，这种形式并没有什么错，真的——但是它并没有什么不对。尽管 Vue 认为上述表单的类型是正确的，但是我们可以添加一些有用的与类型相关的改进:

*   必填字段
*   键入的输入(如`input="email"`
*   键入的表单对象

输入用户元数据。

有许多方法可以使用`[User.json](https://gist.githubusercontent.com/johnfraney/d8fe9868948fdf7bdc599ff0a9c5cbcf/raw/5a32bd7c68e6eda0883241a1947b3d7d6e48c575/user_options.json)`中指定的元数据(在本系列的[第一部分中有详细描述)来改进这种形式。](/nepfin-engineering/use-typescript-to-synchronize-django-rest-framework-and-vue-js-d103cf416e23)

看看这个类型化的表单，然后我们来看看每种元数据的用法是如何改进上面的非类型化表单的:

```
<template>
  <div id="app">
    <h1>User Form</h1>
    <form [@submit](http://twitter.com/submit).prevent="onSubmit">
      <div>
        <label v-text="userMetadata.first_name.label" />
        <input
          v-model="form[userMetadata.first_name.field_name]"
          :maxlength="userMetadata.first_name.max_length"
          :name="userMetadata.first_name.field_name"
          :required="userMetadata.first_name.required"
        >
      </div>
      <div>
        <label v-text="userMetadata.last_name.label" />
        <input
          v-model="form[userMetadata.last_name.field_name]"
          :maxlength="userMetadata.last_name.max_length"
          :name="userMetadata.last_name.field_name"
          :required="userMetadata.last_name.required"
        >
      </div>
      <div>
        <label v-text="userMetadata.email.label" />
        <input
          v-model="form[userMetadata.email.field_name]"
          :maxlength="userMetadata.email.max_length"
          :name="userMetadata.email.field_name"
          :required="userMetadata.email.required"
          :type="userMetadata.email.format"
        >
      </div>
      <div>
        <label v-text="userMetadata.username.label" />
        <input
          v-model="form[userMetadata.username.field_name]"
          :maxlength="userMetadata.username.max_length"
          :name="userMetadata.username.field_name"
          :required="userMetadata.username.required"
        >
      </div>
      <button type="submit">Submit</button>
    </form>
    <pre v-text="userMetadata" />
  </div>
</template><script lang="ts">
import Vue from 'vue'import User from  './metadata/User.json'export default Vue.extend({
  name: 'App', data() {
    return {
      form: {},
      userMetadata: User,
    }
  }, methods: {
    onSubmit() {
      // Pretend a user is created
    },
  },
})
</script>
```

这里的大多数更改都在模板中。在组件本身中，唯一的变化是`userMetadata`，它是从`User.json`创建的类型化对象。(这样使用 JSON 文件，确保你的`[tsconfig.json](https://www.staging-typescript.org/tsconfig#resolveJsonModule)` [有](https://www.staging-typescript.org/tsconfig#resolveJsonModule) `[resolveJSONModule: true](https://www.staging-typescript.org/tsconfig#resolveJsonModule)`。)

模板包括这些更改，所有这些更改都有助于表单反映后端用户模型:

*   `<label v-text>`:表单标签不是手工指定的，而是从用户元数据中提取的。
*   `<input v-model>`:我们可以保证用户表单的键与数据库字段名相同，例如`form[userMetadata.first_name.field_name]`，而不是像`form.firtsName`那样在模板中编写属性访问器。哦，你注意到那个属性访问器中的`fristName`错别字了吗？将元数据用于字段名更能防止输入错误。
*   `<input type>` : API 元数据可以指定每个字段应该使用哪种类型的输入，比如`email`或`number`。这有助于防止验证错误。
*   输入验证属性(`required`，`maxlength`):说到防止验证错误，对使用字段的数据库级约束反映在 JSON 元数据中，我们可以在`<input>`元素中直接使用。`User.json`甚至包括`pattern`，因此您可以在您的客户端表单中重用服务器上定义的正则表达式。这是一个很好的胜利，因为[编写和维护正则表达式不是用户友好的](https://johnfraney.ca/posts/2019/03/24/human-readable-python-regular-expressions/)。
*   `<input name>`:这是一个小的变化，但是它使得在单元测试中选择特定的输入变得容易。

既然我们已经有了一份打印好的表格，为什么不更进一步呢？因为我们的元数据告诉我们这么多关于 API 端点的期望，我们能使用这些数据自动生成表单吗？

亲爱的读者，我们可以。

## 从 JSON 元数据自动生成的表单

这就是健壮元数据真正闪光的地方。通过一些调整，我们可以利用我们的元数据作为一个小的、可维护的模板的全类型组件的基础。

看一看:

```
<template>
  <div id="app">
    <h1>User Form</h1>
    <form [@submit](http://twitter.com/submit).prevent="onSubmit">
      <div v-for="(inputData, inputName) in fields" :key="inputName">
        <label v-text="inputData.label" />
        <input
          v-model="form[inputName]"
          v-bind="inputData.inputAttributes"
        >
      </div>
      <button type="submit">Submit</button>
    </form>
  </div>
</template><script lang="ts">
import Vue from 'vue';import User from  './metadata/User.json'type UserForm = {
  [UserField in keyof typeof User]?: typeof User[UserField]["initial"]
}interface InputAttributes {
  [key: string]: boolean | number | string
}interface InputData {
  inputAttributes: InputAttributes
  label: string
}function convertFieldMetadataToInputData(fieldMetadata: typeof User[keyof typeof User]): InputData {
  const label = fieldMetadata.label
  const inputAttributes: InputAttributes = {
    name: fieldMetadata.field_name,
    required: fieldMetadata.required,
    type: fieldMetadata.type,
  }
  // Add non-universal properties
  if ('max_length' in fieldMetadata) {
    inputAttributes.maxlength = fieldMetadata.max_length
  }
  if ('pattern' in fieldMetadata) {
    inputAttributes.maxlength = fieldMetadata.pattern
  }
  return {
    inputAttributes,
    label,
  }
}export default Vue.extend({
  name: 'App', data() {
    return {
      fields: {
        [User.first_name.field_name]: convertFieldMetadataToInputData(User.first_name),
        [User.last_name.field_name]: convertFieldMetadataToInputData(User.last_name),
        [User.email.field_name]: convertFieldMetadataToInputData(User.email),
        [User.username.field_name]: convertFieldMetadataToInputData(User.username),
      },
      form: {},
    }
  },methods: {
    onSubmit() {
      // Pretend a user is created
    },
  },
});
</script>
```

尽管这个组件比前一个组件多了几行，但是这个组件中的大部分逻辑都可以删除，并在应用程序的每个表单中共享。

组件中发生了相当多的新变化，所以让我们来看看重要的变化。

一、形体:

```
<div v-for="(inputData, inputName) in fields" :key="inputName">
  <label v-text="inputData.label" />
  <input
    v-model="form[inputName]"
    v-bind="inputData.inputAttributes"
  >
</div>
```

就这样吗？你打赌。我们不需要指定每个字段，只需在组件中做一点工作，就可以遍历`fields`。

(我应该注意，这个实现只适用于 all- `<input>`表单。如果您的表单包含`<select>`元素、复选框或单选按钮，您可能需要创建一个单独的组件来输出正确的表单域元素。)

我们还添加了一些额外的类型接口:

*   `UserForm`使用一些 TypeScript 魔法(特别是[索引类型和索引签名](https://www.staging-typescript.org/docs/handbook/advanced-types.html#index-types-and-index-signatures))来生成表示用户表单的类型，使用来自用户元数据的`initial`值。
*   对于`InputAttributes`，我们在元数据中指定的 [HTML 属性](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/Input#Attributes)采用三种类型中的一种:布尔属性，如`[required](https://developer.mozilla.org/en-US/docs/Web/HTML/Attributes/required)`；数量等属性`[maxlength](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/Input#htmlattrdefmaxlength)`；以及属性字符串，如`[type](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/Input#attr-type)`。
*   `InputData`是一个表示生成表单所需信息的对象。我们对`<label>`元素使用 label 属性，并使用 Vue 的`[v-bind](https://vuejs.org/v2/api/#vm-attrs)`将`inputAttributes`传递给`<input>`元素，就像`v-bind="inputAttributes"`一样。这样我们就不必单独指定每个道具。

为了获得可用的`inputAttributes`，我们需要将元数据字段的名称映射到正确的 HTML 属性名称。我们还需要对不是出现在每个元数据节点上的属性进行空值检查，因此输入元素也是如此。这项工作由`convertFieldMetadataToInputData`处理。

但是为什么我们首先需要`convertFieldMetadataToInputData`？

嗯，是因为我没有提前计划。如果 API 元数据使用这些 HTML 属性所期望的字符串，我们将能够直接使用它们，而不是转换它们。我真惭愧，没有早点想到这一点！

# 总结

我们做到了！稍加修改，我们就能够使用来自类似 REST 的 JSON API 的元数据生成完全类型化的表单。这篇文章也是一个例子，说明保持 web 应用程序的前端和后端同步是多么困难——即使试图展示一种方法来做到这一点。

不过，如果仔细规划，利用 API 元数据可以简化 SPA 应用程序，并减少 JavaScript web 应用程序中的样板文件和难以同步的代码。我说的“仔细计划”是指比我的计划更仔细的计划。

**快乐编码！**
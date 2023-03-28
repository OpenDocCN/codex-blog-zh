# 使用 registerForActivityResult 的 Android 运行时权限

> 原文：<https://medium.com/codex/android-runtime-permissions-using-registerforactivityresult-68c4eb3c0b61?source=collection_archive---------1----------------------->

![](img/7b12b96778246077a73ee2903ea5326e.png)

Android 最近弃用了其旧的 ActivityResult API，并通过 AndroidX Activity 1.2.0-alpha02 引入了新的 API。这使得请求运行时权限变得更加容易。

有了新的 API，开发人员现在可以注册结果，启动结果，然后处理系统产生的结果。

# 但是为什么呢？

当为了一个结果而开始一个活动时，很可能你的过程和活动会因为在像相机使用这样的内存密集型操作中内存不足而被破坏。因此，活动结果 API 将结果回调与代码中启动其他活动的位置分离开来。

因此，不幸的是，当流程或活动被重新创建时，回调必须每次都注册它，即使启动其他活动的逻辑只是基于用户输入或其他业务逻辑。

# **有什么不同？**

以前，我们必须通过将请求代码传递给 requestPermissions()方法来管理请求代码，同时请求权限。然后在 OnRequestPermissionResult()方法中获取结果时使用请求代码，如下所示。

```
ActivityCompat.**requestPermissions**(context,permissionArray,
                                  requestCode)override fun **onRequestPermissionsResult**(requestCode: Int,
        permissions: Array<String>, grantResults: IntArray) {
    when (requestCode) {
        REQUEST_CODE -> {
            if ((grantResults.isNotEmpty() &&
              grantResults[0] == PackageManager.PERMISSION_GRANTED){
                // Permission is granted.
            } else {
               //permission is denied.
            }
            return
        }
    }
}
```

有了新的活动结果 API，我们不必自己管理请求代码，因为系统会在内部自行处理。

新的活动结果 API 提供了**registerForActivityResult()**API 来注册结果回调。它接受一个 **ActivityResultContract** 和一个 **ActivityResultCallback** ，并返回一个 ActivityResultLauncher，使用它我们可以启动其他活动，如下所示。

```
class MainActivity : AppCompatActivity() {

    override fun **onCreate**(savedInstanceState: Bundle?) **{**
        super.onCreate(savedInstanceState)
        setContentView(R.layout.*activity_main*)

        findViewById<Button>(R.id.*button*).setOnClickListener {activityResultLauncher.launch(Manifest.permission.*CAMERA*)
        } **}**

    private val **activityResultLauncher** =
        **registerForActivityResult**(
           ActivityResultContracts.RequestPermission()){isGranted **->** // Handle Permission granted/rejected
             if (isGranted) {
                // Permission is granted
             } else {
                // Permission is denied
             }
           }}
```

*   **ActivityResultContract** 指定产生结果所需的输入类型。在上面的 ActivityResultContracts 示例中。使用 RequestPermission()，这意味着活动启动器将需要 android 权限作为输入。
*   **ActivityResultCallback** 指定我们如何处理用户对权限请求的响应
*   当调用 launch()方法并输入相机权限时，将显示一个请求相机权限的权限对话框。根据用户对权限的响应，系统调用 activityResultCallback。

类似地，通过传递权限数组而不是单个权限作为输入，可以同时请求多个权限，我们得到一个以权限作为键的 MutableMap，并在 activityResultCallback 中以值的形式授予结果。

```
class MainActivity : AppCompatActivity() {

    override fun **onCreate**(savedInstanceState: Bundle?) **{**
        super.onCreate(savedInstanceState)
        setContentView(R.layout.*activity_main*)

        findViewById<Button>(R.id.*button*).setOnClickListener {activityResultLauncher.launch(
           arrayOf(Manifest.permission.*CAMERA,* Manifest.permission.*READ_EXTERNAL_STORAGE)             
           )* 
        } **}**

    private val **activityResultLauncher** =
        **registerForActivityResult**(
           ActivityResultContracts.RequestMultiplePermissions())   
             { permissions **->
             **   // Handle Permission granted/rejected
               permissions.entries.*forEach {*valpermissionName = it.key
                  val isGranted = it.value
                  if (isGranted) {
                     // Permission is granted
                  } else {
                     // Permission is denied
                  }
              }           }}
```

您可以使用下面提到的链接查看官方文档，以便进一步阅读:

[https://developer . Android . com/training/basics/intents/result](https://developer.android.com/training/basics/intents/result)

[https://developer . Android . com/training/permissions/requesting](https://developer.android.com/training/permissions/requesting)
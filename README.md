# SGX_Hello_World
My first SGX application demo Hello World.

This project is modified on the [SGX Sample Code](https://github.com/intel/linux-sgx/tree/master/SampleCode/SampleEnclave). It is just for testing the installation of SGXSDK. 

Find more more details on [MyBlog](https://fanglk.cn/intel-sgx-linux-start/).

## The files modified

- Enclave.edl

```
trusted{
        public void printf_helloworld();
};
```

- Enclave.cpp

```c++
void printf_helloworld()
{
    printf("Hello World\n");
}
```

- Enclave.h

```c++
void printf_helloworld();
```

- App.cpp

由于我们不需要Edger8rSyntax和TrustedLibrary相关的函数调用，所以我们把main函数中与这两部分相关的函数调用删除，也就是把下面部分删除：

```c++
/* Utilize edger8r attributes */
    edger8r_array_attributes();
    edger8r_pointer_attributes();
    edger8r_type_attributes();
    edger8r_function_attributes();
    
    /* Utilize trusted libraries */
    ecall_libc_functions();
    ecall_libcxx_functions();
    ecall_thread_functions();
```

在main函数中的Enclave创建和销毁之间插入我们定义的`printf_helloworld`函数的调用代码，也就是把以下代码放到 `sgx_destroy_enclave(global_eid);`之前，注意，`global_eid`参数是必须的，是enclave的id。

```c++
printf_helloworld(global_eid);
```

我们也可以将main函数后面的无关代码给删了，也就是删除以下代码

```c++
printf("Info: SampleEnclave successfully returned.\n");
     
    printf("Enter a character before exit ...\n");
    getchar();
```

- Makefile文件修改

**修改SGX_SDK路径**

默认SDK路径是`/opt/intel/sgxsdk`，如果不是需要修改路径

```makefile
SGX_SDK ?= /opt/intel/sgxsdk
```

**修改编译模式**

在未开启硬件支持下，需要修改编译模式为模拟环境。

```makefile
SGX_MODE = SIM
```


**修改不可信代码编译的源文件**

```makefile
App_Cpp_Files := $(wildcard App/*.cpp) $(wildcard App/Edger8rSyntax/*.cpp) $(wildcard App/TrustedLibrary/*.cpp)
```

**修改可信代码编译的源文件**

```makefile
Enclave_Cpp_Files := $(wildcard Enclave/*.cpp) $(wildcard Enclave/Edger8rSyntax/*.cpp) $(wildcard Enclave/TrustedLibrary/*.cpp)
```

## Make

- `make` make
- `./app` run


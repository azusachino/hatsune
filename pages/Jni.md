- #CS
- ## Contents
	- ### JVM OnLoad
		- ```c++
		  #include <jni.h>
		  #include <myrica.h>
		  
		  #include <iostream>
		  
		  // save global jvm pointer for futher usage
		  JavaVM *jvm_global_ = nullptr;
		  
		  // main jni clazz
		  static const char *jni_clazz_impl_ = "cn/az/code/jni/HelloJni";
		  
		  // native methods
		  static JNINativeMethod jni_methods_[] = {
		      {(char *)"native_say", (char *)"()V", (void *)jni_native_say}};
		  
		  /**
		   * @brief Register several native methods for one class.
		   */
		  static int registerNativeMethods(JNIEnv *env, const char *className,
		                                   JNINativeMethod *gMethods, int numMethods)
		  {
		      jclass clazz = env->FindClass(className);
		      if (clazz == NULL)
		      {
		          return JNI_FALSE;
		      }
		      if (env->RegisterNatives(clazz, gMethods, numMethods) < 0)
		      {
		          return JNI_FALSE;
		      }
		      return JNI_TRUE;
		  }
		  
		  JNIEXPORT jint JNICALL JNI_OnLoad(JavaVM *jvm, void* reserved) {
		    
		    std::cout << "jvm OnLoad" << std::endl;
		    
		    jvm_global_ = jvm;
		    
		    // Real Jni Operation Instance
		    JNIEnv *jni_env = nullptr;
		    // Get current JniEnv pointer
		    jint r = jvm_global_->GetEnv(reinterpret_cast<void **>(&jni_env), JNI_VERSION_1_8);
		    // if fail, return
		    if (r == JNI_FALSE) {
		      std::cerr << "fail to get env" << std::endl;
		      return JNI_FALSE;
		    }
		    
		    // register native methods
		    jint ret = registerNativeMethods(jni_env, jni_clazz_impl_, jni_methods_, sizeof(jni_methods_) / sizeof(jni_methods_[0]));
		    if (JNI_TRUE == ret)
		        std::cout << "native methods registered" << std::endl;
		    
		    // must return valid JNI Version
		    return JNI_VERSION_1_8;
		  }
		  ```
	- ### JVM OnUnLoad
		- ```c++
		  JNIEXPORT jint JNICALL OnUnload(JavaVM *vm, void *reserved)
		  {
		      std::cout << "jvm OnUnload" << std::endl;
		      return JNI_VERSION_1_8;
		  }
		  ```
	- TODO ### Attach JVM to Thread
		- ```c++
		  ```
	- ### `extern "C"` Implementation
		- ```c++
		  #include <iostream>
		  
		  #include "myrica.h"
		  
		  extern "C" JNIEXPORT void JNICALL jni_native_say(JNIEnv *, jobject)
		  {
		      std::cout << "Hello Jni" << std::endl;
		  }
		  ```
	- TODO ### Deal with callback
		- ```c++
		  ```
	- ### Load Library
		- Two approaches to set `java.library.path`
			- VM Args - `java -Djava.library.path=/usr/local/app -jar app.jar`
			- Set System Property - `System.setProperty("java.library.path", "/usr/local/app");`
		- Differences
			- 如果想在程序中加载一些库文件，使用第一种方式指定 `java.library.path` 属性时可以正常载入，而使用第二中方式就不行。
			- `java.library.path` 只有在JVM启动的时候读取一次，因此在代码中更改 `java.library.path` 是不起任何作用的。
			- 可以在代码中使用 `System.load("/usr/local/app")` 来加载绝对地址指定的本地库。
		- `LD_LIBRARY_PATH`
			- JVM启动时，会使用系统变量 `LD_LIBRARY_PATH` 的值来初始化`java.library.path` 属性。
		- `-Djava.library.path` 和  `LD_LIBRARY_PATH` 的区别
			- 使用  `java -Djava.library.path=/usr/local/app` 的方式设置的话，会**覆盖**默认值。
			- 使用 `export  LD_LIBRARY_PATH= /usr/local/app` 的方式设置的话，会**追加**其值到默认值中。
- ## References
	- [myrica - jni-server](https://github.com/azusachino/myrica)
	- [java library path](https://blog.csdn.net/codepython/article/details/42718003)
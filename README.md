# pyflann
Research NN searching method FLANN
近邻近似搜索算法已经集成到OpenCV中，在OpenCV中有一个模块就是flann，其中基本就是在FLANN库的基础上进行简单更改集成的。有的时候我们任务中如果想单独使用该模块，可以直接对其编译成动态链接库，编写接口进行调用。本项目就是对FLANN进行编译，然后直接进行相应调用使用的测试。

## Compule flann by yourself

1. download source code 
2. make sure install the following packages:
	cmake
    libhdf5-dev
    libgtest-dev :这个库最好是自己下载只有进行自己编译，这个编译过程比较好
    mpi 和mp等库
3. update CMakeList.txt
	for example: I haven't install matlab in my machine 
and I set the Matlab bind from **ON** to **OFF**.
4. cmake ..  
   sudo make all
   NOTE: make process may have a long time in virtul machine, and the process may be halt in somewhere, please wait...
   sudo make install
5. 检查是否有相应的python接口，如果有的话就太棒了。所幸的是flann已经有实现python的接口，不过我们可以学习一下python如何调用c,c++的动态连接库的。
    * 包括数据类型的对应关系的设定,和动态库的加载
   ```python
    from ctypes import (Structure, c_char_p, c_int, c_float, c_uint, c_long,
                    c_void_p, cdll, POINTER)
    def load_flann_library():
	    root_dir = os.path.abspath(os.path.dirname(__file__))
	    libnames = ['libflann.so']
	    libdir = 'lib'
	    if sys.platform == 'win32':
		libnames = ['flann.dll', 'libflann.dll']
	    elif sys.platform == 'darwin':
		libnames = ['libflann.dylib']
	    while root_dir is not None:
		for libname in libnames:
		    try:
			#print 'Trying ',os.path.join(root_dir,'lib',libname)
			flannlib = cdll[os.path.join(root_dir, libdir, libname)]
			return flannlib
		    except Exception:
			pass
		    try:
			flannlib = cdll[os.path.join(root_dir, 'build', libdir, libname)]
			return flannlib
		    except Exception:
			pass
		tmp = os.path.dirname(root_dir)
		if tmp == root_dir:
		    root_dir = None
		else:
		    root_dir = tmp
	    # if we didn't find the library so far, try loading without
	    # a full path as a last resort
	    for libname in libnames:
		try:
		    #print 'Trying',libname
		    flannlib = cdll[libname]
		    return flannlib
		except:
		    pass
	    return None
    ```
    * 定义接口的返回类型和参数的对应关系
## FLANN库
[Flann 参考手册](http://www.cs.ubc.ca/research/flann/uploads/FLANN/flann_manual-1.8.4.pdf)
Muja, M., & Lowe, D. G. (2014). Scalable nearest neighbor algorithms for high dimensional data. IEEE Transactions on Pattern Analysis & Machine Intelligence, 36(11), 2227-2240.
FLANN库全称是Fast Library for Approximate Nearest Neighbors，它是目前最完整的（近似）最近邻开源库。不但实现了一系列查找算法，还包含了一种自动选取最快算法的机制。

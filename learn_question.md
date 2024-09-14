  

在ros2中colcon项目的时候因为配置serial_driver库出现libfmt.o链接失败的解决方法

重新编译fmt库

```shell
git clone https://github.com/fmtlib/fmt.git
cd fmt
mkdir build && cd build
cmake .. -DCMAKE_POSITION_INDEPENDENT_CODE=TRUE
make 
sudo make install
```


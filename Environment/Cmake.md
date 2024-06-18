# 指定安装位置

```bash
cmake .. -DCMAKE_INSTALL_PREFIX=/path/to/your_prefix
```

# linux下默认安装位置

- /usr
- /usr/local


# CMakeLists调用

强制指定包版本

```cmake
find_package(package_name package_version EXACT)
```



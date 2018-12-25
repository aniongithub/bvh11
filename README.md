# bvh11

A tiny C++11 library for reading BVH motion capture data.

![](./docs/sample.gif)

## Dependencies

- C++11 standard library
- Eigen <http://eigen.tuxfamily.org/>

### Additional Dependencies for Demos

- three-dim-util <https://github.com/yuki-koyama/three-dim-util/>

## Usage

### Build and Install

```
$ git clone https://github.com/yuki-koyama/bvh11.git --recursive
$ mkdir build
$ cd build
$ cmake ../bvh11
$ make
$ make install
```

### Import BVH

```
#include <bvh11.hpp>

int main()
{
  bvh11::BvhObject("/path/to/bvh/data.bvh");
  
  // Do something
  
  return 0;
}
```

## License

MIT License.

## Contributing

Contributions are welcome.

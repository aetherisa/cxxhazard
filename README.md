# cxxhazard

A small C++17 hazard pointer implementation written as a learning project.

The goal of this project is to study how hazard pointers can be used for safe memory reclamation in lock-free data structures. It includes a basic hazard pointer interface, retired-object reclamation, and a simple stack example.

## Example

A simplified usage pattern:

```cpp
bool pop(T& dest)
{
    auto hazard = make_hazard();

    node* old_head;
    do {
        old_head = hazard.protect(_head);

        if (old_head == nullptr)
            return false;

    } while (!_head.compare_exchange_strong(old_head, old_head->_next));

    hazard.unprotect();

    dest = *old_head->_data;

    retire(old_head, [old_head]() {
        delete old_head->_data;
        delete old_head;
    });

    return true;
}
```

For a complete example, see [`test/test01.cpp`](test/test01.cpp).

## Project Structure

| File                                             | Purpose                         |
| ------------------------------------------------ | ------------------------------- |
| [`hazard.hpp`](include/cxxhazard/hazard.hpp)     | Main public header              |
| [`ptr.hpp`](include/cxxhazard/ptr.hpp)           | Hazard pointer interface        |
| [`resource.hpp`](include/cxxhazard/resource.hpp) | Internal hazard pointer storage |
| [`reclaim.hpp`](include/cxxhazard/reclaim.hpp)   | Retired object reclamation      |
| [`domain.hpp`](include/cxxhazard/domain.hpp)     | Domain/base class support       |
| [`fwd.hpp`](include/cxxhazard/fwd.hpp)           | Forward declarations            |

## Status

This project is mainly for studying lock-free memory reclamation. It is not intended to be a production-ready library.

## TODO

* Add more tests
* Add more examples
* Improve the internal hazard pointer lookup structure

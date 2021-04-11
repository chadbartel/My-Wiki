# Python

This is the root wiki page for Python.

## Table of Contents

* [Return to the main page...](../../README.md)
* [Summary](#summary)
* [Install Python on Ubuntu](#install-python-on-ubuntu)

### Summary

From [Wikipedia](https://en.wikipedia.org/wiki/Python_(programming_language)):

> **Python** is an interpreted, high-level and general-purpose programming language. Python's design philosophy emphasizes code readability with its notable use of significant indentation. Its language constructs and object-oriented approach aim to help programmers write clear, logical code for small and large-scale projects. Python is dynamically-typed and garbage-collected. It supports multiple programming paradigms, including structured (particularly, procedural), object-oriented and functional programming. Python is often described as a "batteries included" language due to its comprehensive standard library.

### Install Python on Ubuntu

If you need to install a version of Python on Ubuntu that isn't available natively, you can install it via the [deadsnakes PPA](https://launchpad.net/~deadsnakes/+archive/ubuntu/ppa) (personal package archive).

 1. Add the repository:

    ```bash
        sudo add-apt-repository ppa:deadsnakes/ppa
    ```

 2. Update your system:

    ```bash
        sudo apt-get update
    ```

 3. Install *almost* any non-native version of Python you need:

    ```bash
        sudo apt-get install python3.7
    ```

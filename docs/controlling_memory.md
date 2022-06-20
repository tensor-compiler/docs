# Controlling Memory

When using the TACO C++ library, the typical usage is to declare your input
`taco::Tensor` structures, then add data to these structures using the `insert`
method. This is wasteful if the data is already loaded into memory in a
compatible format; TACO can use this data directly without copying it. Below
are some usage examples for common situations where a user may want to do this.

## CSR Matrix

A two-dimensional CSR matrix can be created using three arrays:

* `rowptr` (array of `int`): list of indices in `colidx` representing starts of rows
* `colidx` (array of `int`): list of column indices of non-zero values
* `vals` (array of `T` for `Tensor<T>`): list of non-zero values corresponding to columns in `colidx`

The `taco::makeCSR<T>` function takes these arrays and creates a
`taco::Tensor<T>`. The following example constructs a 5x10 matrix populated
with a few values.

```c++
int *rowptr = new int[6]{0, 2, 4, 4, 4, 7};
int *colidx = new int[7]{3, 5, 0, 7, 7, 8, 9};
double *values = new double[7]{0.1, 0.2, 0.3, 0.4, 0.5, 0.6, 0.7};
Tensor<double> A = makeCSR("A", {5, 10}, rowptr, colidx, values);
```

## CSC Matrix

Similarly, a two-dimensional CSC matrix can be created from the appropriate
arrays using the `taco::makeCSC<T>` function. This example constructs the same
5x10 matrix from the CSR example above, but in CSC format.

```c++
int *colptr = new int[11]{0, 1, 1, 1, 2, 2, 3, 3, 5, 6, 7};
int *rowidx = new int[7]{1, 0, 0, 1, 4, 4, 4};
double *values = new double[7]{0.3, 0.1, 0.2, 0.4, 0.5, 0.6, 0.7};
Tensor<double> B = makeCSC("B", {5, 10}, colptr, rowidx, values);
```

## Dense Vectors

For single-dimension dense vectors, you can use an array of values (of type `T`
for a `Tensor<T>`). There is no helper function for this (like `makeCSR` or
`makeCSC`), but it can be done. This example constructs a 1x10 dense vector.

```c++
// Create an array of double values.
double *x_values = new double[10];
for (int i = 0; i < 10; i++) {
    x_values[i] = i;
}

// Create the Tensor and set its storage to our array of values.
Tensor<double> x({10}, Dense);
Array x_array = makeArray<double>(x_values, 10);
TensorStorage x_storage = x.getStorage();
x_storage.setValues(x_array);
x.setStorage(x_storage);
```

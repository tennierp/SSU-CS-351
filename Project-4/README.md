# Project 6: CUDA

## Part 1: CUDA iota function

Implemented a CUDA kernel to fill an array with values. The kernel uses parallel threads where each thread computes one array element.

### Question: Are the results what you expected? Speculate as to why it looks like CUDA isn't a great solution for this problem.

No, I expected that the GPU would be faster, but I see now that the GPU is slower because the
iota operation is very simple it allows the CPU to do its job, and it's taking longer because of the overhead of copying
all the data to and from the GPU and launching the kernel takes more time than just doing the simple
computations on the CPU.

## Part 2: Julia Set Generator

Converted a CPU-based Julia set generator to CUDA. Each thread computes one pixel by iterating a complex number equation until it either escapes or reaches the maximum iterations.

### Generated Mandelbrot Set

![Mandelbrot Set](julia.ppm)
### Are the results what you expected? Speculate as to why it looks like CUDA isn't a great solution for this problem.

No, I expected that the GPU would be faster, but I see now that the GPU is slower because the
iota operation is very simple it allows the CPU to do its job, and it's taking longer because of the overhead of copying
all the data to and from the GPU and launching the kernel takes more time than just doing the simple
computations on the CPU.
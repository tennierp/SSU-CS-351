# SSU Computer Architecture 351
Project repository for Computer Architecture.

## Question 1: Which program is fastest? Is it always the fastest?
The fastest program depends on the optimization and node sizes used with the make command.

Using -02 proves that
alloca.out is usually the fastest and malloc.out is very close to it.

Without optimization and just using -g
malloc.out is the fastest and alloca.out turns out to be in second place.

So, the fastest program is not always the same, it depends strictly on the configuration in the make setup.

## Question 2: Which program is slowest? Is it always the slowest?

list.out is the slowest, but new.out is sometimes slower.

Without optimization (-g) list.out is consistently the slowest.
With optimization (-O2) and small nodes:
new.out becomes the slowest and list.out is second slowest

With large nodes and using (-O2):
list.out is again the slowest.

Therefore the slowest program is not always the same just like the fastest. It depends on the optimizations (-g) and (-02) used with make.

## Question 3: Was there a trend in program execution time based on the size of data in each Node? If so, what, and why?

Yes, there is a clear trend, larger node sizes result in longer execution times.

### Data Comparison:

#### Small Nodes vs Large Nodes, 1M blocks, and the -O2 optimization:

| Program | Small Nodes | Large Nodes | Slowdown Factor |
|---------|-------------|-------------|-----------------|
| alloca.out | 0.201s | 1.789s | ~9x slower |
| malloc.out | 0.236s | 1.791s | ~8x slower |
| new.out | 0.324s | 1.817s | ~5x slower |
| list.out | 0.307s | 1.900s | ~6x slower |

This happens most likely because the hashing operation has to process more bytes with larger nodes, and since the nodes take more memory, it causes more cache misses.

## Question 4: Was there a trend in program execution time based on the length of the block chain?

Yes, execution time seems to scale linearly with the number of blocks in the chain.

Data: Scaling from 1M to 10M blocks (-O2, small nodes):

| Program | 1M blocks | 10M blocks | Scaling Factor |
|---------|-----------|------------|----------------|
| alloca.out | 0.201s | 2.097s | ~10x |
| malloc.out | 0.236s | 2.470s | ~10x |
| new.out | 0.324s | 3.076s | ~11x |
| list.out | 0.307s | 3.295s | ~10x |

Since the scaling factor from 1 million to 10 million blocks is almost exactly 10x on the scaling factor, it proves that this is a linear algorithm.

## Question 5: Consider heap breaks, what's noticeable? Does increasing the stack size affect the heap? Speculate on any similarities and differences in programs.

### Heap Break Outputs:

#### 100K blocks:
| Program | brk() calls |
|---------|-------------|
| alloca.out | 69 |
| malloc.out | 542 |
| new.out | 559 |
| list.out | 559 |

#### 1M blocks:
| Program | brk() calls |
|---------|-------------|
| alloca.out | 69 |
| malloc.out | 4,797 |
| new.out | 4,975 |
| list.out | 4,975 |

The noticeable outputs for each program was that alloca.out had constant heap break around 69 to 70 regardless of the block count. But malloc.out, new.out, and list.out used almost 10x more blocks making the heap expand as the programs requested more memory. new.out and list.out also had identical break counts even when scaling by a million.
The stack size doesn't seem to affect the heap, when using ulimit -s unlimited" alloca.out can use as much stack as needed so it doesn't affect the heap and keeps it consistant at 69 heap breaks. new.out and list.out had identical break counts because std::list uses operator new for its node allocations, so they're using the same exact memory allocator.

## Question 6: Diagram of malloc.cpp or alloca.cpp Nodes
I gave claude.ai information about the malloc.cpp program and had it generate a diagram for README.md
### malloc.cpp Node Structure Diagram

```
Linked List Structure:
┌─────────┐      ┌─────────┐      ┌─────────┐
│  head   │─────>│  Node   │─────>│  Node   │─────> ... ─────> nullptr
└─────────┘      └─────────┘      └─────────┘                   (tail->next)
                      │                 │
                      │                 │
                    ┌─▼──────────────┐┌─▼──────────────┐
                    │  Node Memory   ││  Node Memory   │
                    │   (on heap)    ││   (on heap)    │
                    └────────────────┘└────────────────┘


Detailed Node Structure (with 6 bytes of data):

Memory Layout on Heap:
┌────────────────────────────────────────────────┐
│              Node Object                        │
├────────────────────────────────────────────────┤
│  Node* next         (8 bytes on 64-bit)        │  ← pointer to next Node
├────────────────────────────────────────────────┤
│  uint8_t* bytes     (8 bytes on 64-bit)        │  ← pointer to data array
├────────────────────────────────────────────────┤
│  size_t numBytes    (8 bytes on 64-bit)        │  ← stores value: 6
└────────────────────────────────────────────────┘
Total Node size: 24 bytes

                    │
                    │ bytes pointer points to:
                    ▼
┌────────────────────────────────────────────────┐
│         Data Array (6 bytes)                   │
├───┬───┬───┬───┬───┬───┐                       │
│[0]│[1]│[2]│[3]│[4]│[5]│                       │
└───┴───┴───┴───┴───┴───┘                       │
└────────────────────────────────────────────────┘
  ↑
  │
bytes pointer points here (to first byte)

Total memory allocated: 24 bytes (Node) + 6 bytes (data) = 30 bytes
Plus malloc overhead: ~16-32 bytes per allocation
Total per Node: ~62 bytes
```

## Question 7: For each program, were any of these tasks the same? Which one(s) were different?

The two tasks that seemed the same across each of the programs was the initialization and processing parts. All four of the programs construct the Nodes with the same random data, build the same linked list structure, and use the same hashing algorithm when moving through the list. The only difference in them is that they are using different memory allocations. list.cpp uses the std::list, new.cpp uses the new operator, malloc.cpp uses malloc(), and alloca.cpp uses alloca(). The allocation differences when running the program show the variations that each memory allocation provides. It shows how when using -02 alloca.out was the fastest and when just using -g malloc.out seemed to be the fastest.

## Question 8: As the size of data in a Node increases, does the significance of allocating the node increase or decrease?

The significance of allocating the node shows that it decreases as the size of data increases. With small nodes the allocation overhead is what made alloca so much faster then new (the slowest). With larger nodes all of the programs ran increasingly slower and gave them a very small window of difference between the fastest and slowest. From seeing the data it shows that the hashing time is growing linearly with the amount of data thats given. So for small data structures using the optimization tools was significantly more useful for the programs compared to when using the large data.

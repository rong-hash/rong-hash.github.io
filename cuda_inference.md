# UCAS GPU Programming HW1: PointNet Inference in CUDA

## Kernel

1. Try to increase the parallelism of the kernel. For example, add batch as a dimension in the grid/block definition.
2. Decrease the memory access to global memory. Use shared memory as much as possible. For example, TILED matrix multiplication. I designed broadcastMatrixMultiplyShared to handle convolution operation, matrix multiplication for linear layer and batchMatrixMultiplyShared to handle batch matrix multiplication operation.
    ```C++
    __global__ void matrixMultiplyShared(half* __restrict__ A, half* __restrict__ B, half* __restrict__ C, half* __restrict__ bias,
        int numARows, int numAColumns,
        int numBRows, int numBColumns,
        int numCRows, int numCColumns) {
        //@@ Insert code to implement matrix multiplication here
        //@@ You have to use shared memory for this MP
        __shared__ half ds_A[TILE_WIDTH][TILE_WIDTH];
        __shared__ half ds_B[TILE_WIDTH][TILE_WIDTH];
        int bx = blockIdx.x;
        int by = blockIdx.y;
        int tx = threadIdx.x;
        int ty = threadIdx.y;
        int Row = by * TILE_WIDTH + ty;
        int Col = bx * TILE_WIDTH + tx;
        half Cvalue = 0.0;
        for (int m = 0; m < (numAColumns - 1) / TILE_WIDTH + 1; ++m) {
            if (Row < numARows && m * TILE_WIDTH + tx < numAColumns) {
                ds_A[ty][tx] = A[Row * numAColumns + m * TILE_WIDTH + tx];
            } else {
                ds_A[ty][tx] = __float2half(0.0f);
            }
            if (m * TILE_WIDTH + ty < numBRows && Col < numBColumns) {
                ds_B[ty][tx] = B[(m * TILE_WIDTH + ty) * numBColumns + Col];
            } else {
                ds_B[ty][tx] = __float2half(0.0f);
            }
            __syncthreads();
            for (int k = 0; k < TILE_WIDTH; ++k) {
                Cvalue = __hadd(Cvalue, __hmul(ds_A[ty][k], ds_B[k][tx]));
            }
            __syncthreads();
        }
        if (Row < numCRows && Col < numCColumns) {
            C[Row * numCColumns + Col] = __hadd(Cvalue, bias[Row]);
        }
    }

    // Compute C = A * B in batch in shared memory by tiled matrix multiplication
    __global__ void batchMatrixMultiplyShared(half* __restrict__ A, half* __restrict__ B, half* __restrict__ C,
        int numARows, int numAColumns,
        int numBRows, int numBColumns,
        int numCRows, int numCColumns, int batch) {
        __shared__ half ds_A[TILE_WIDTH][TILE_WIDTH];
        __shared__ half ds_B[TILE_WIDTH][TILE_WIDTH];
        
        int bx = blockIdx.x;
        int by = blockIdx.y;
        int bz = blockIdx.z;
        int tx = threadIdx.x;
        int ty = threadIdx.y;
        
        int Row = by * TILE_WIDTH + ty;
        int Col = bx * TILE_WIDTH + tx;
        
        // Calculate the starting point for this batch
        int batchOffset = bz * numARows * numAColumns;
        A += batchOffset;
        B += bz * numBRows * numBColumns;
        C += bz * numCRows * numCColumns;
        
        half Cvalue = 0.0;
        
        for (int m = 0; m < (numAColumns - 1) / TILE_WIDTH + 1; ++m) {
            if (Row < numARows && m * TILE_WIDTH + tx < numAColumns) {
                ds_A[ty][tx] = A[Row * numAColumns + m * TILE_WIDTH + tx];
            } else {
                ds_A[ty][tx] = 0.0;
            }
            
            if (m * TILE_WIDTH + ty < numBRows && Col < numBColumns) {
                ds_B[ty][tx] = B[(m * TILE_WIDTH + ty) * numBColumns + Col];
            } else {
                ds_B[ty][tx] = 0.0;
            }
            
            __syncthreads();
            
            for (int k = 0; k < TILE_WIDTH; ++k) {
                Cvalue = __hadd(Cvalue, __hmul(ds_A[ty][k], ds_B[k][tx]));
            }
            
            __syncthreads();
        }
        
        if (Row < numCRows && Col < numCColumns) {
            C[Row * numCColumns + Col] = Cvalue;
        }
    }

    // Compute C = A * B in batch in shared memory by tiled matrix multiplication
    __global__ void broadcastMatrixMultiplyShared(half* __restrict__ A, half* __restrict__ B, half* __restrict__ C, half* __restrict__ bias,
        int numARows, int numAColumns,
        int numBRows, int numBColumns,
        int numCRows, int numCColumns, int batch) {
        __shared__ half ds_A[TILE_WIDTH][TILE_WIDTH];
        __shared__ half ds_B[TILE_WIDTH][TILE_WIDTH];
        
        int bx = blockIdx.x;
        int by = blockIdx.y;
        int bz = blockIdx.z;
        int tx = threadIdx.x;
        int ty = threadIdx.y;
        
        int Row = by * TILE_WIDTH + ty;
        int Col = bx * TILE_WIDTH + tx;
        
        // Calculate the starting point for this batch
        B += bz * numBRows * numBColumns;
        C += bz * numCRows * numCColumns;
        
        half Cvalue = 0.0;
        
        for (int m = 0; m < (numAColumns - 1) / TILE_WIDTH + 1; ++m) {
            if (Row < numARows && m * TILE_WIDTH + tx < numAColumns) {
                ds_A[ty][tx] = A[Row * numAColumns + m * TILE_WIDTH + tx];
            } else {
                ds_A[ty][tx] = 0.0;
            }
            
            if (m * TILE_WIDTH + ty < numBRows && Col < numBColumns) {
                ds_B[ty][tx] = B[(m * TILE_WIDTH + ty) * numBColumns + Col];
            } else {
                ds_B[ty][tx] = 0.0;
            }
            
            __syncthreads();
            
            for (int k = 0; k < TILE_WIDTH; ++k) {
                Cvalue = __hadd(Cvalue, __hmul(ds_A[ty][k], ds_B[k][tx]));
            }
            
            __syncthreads();
        }
        
        if (Row < numCRows && Col < numCColumns) {
            C[Row * numCColumns + Col] = __hadd(Cvalue, bias[Row]);
        }
    }
    ```
3. `__restrict__` is used to indicate that the pointers are not shared. This is for the compiler optimization.
   ```C++
   half* __restrict__ A, half* __restrict__ B, half* __restrict__ C, ...
   ```
4. Parameter adjustment. Try to increase the TILE_WIDTH and block size. 
    ```C++
    #define TILE_WIDTH 32
    #define BLOCK_SIZE 1024
    ```
    Note that for most GPU, the maximum block size is 1024. But the maximum grid size is much larger.
5. Loop unrolling is used to reduce the overhead of the loop. (Very little effect)
    ```C++
    #pragma unroll
    for (int k = 0; k < TILE_WIDTH; ++k) {
        Cvalue = __hadd(Cvalue, __hmul(ds_A[ty][k], ds_B[k][tx]));
    }
    ```
6. Kernel fusion. Merge matmul, batchnorm, and relu into one kernel. (todo)
7. Tensor core for matmul optimization. (todo)


## Memory

1. Use pinned memory instead of page-locked memory.
    ```C++
    cudaHostAlloc(&input, numPoints * sizeof(half), cudaHostAllocDefault);
    ```
   
2. Use stream to overlap the host and device memory transfer.
    ```C++
    cudaStreamCreate(&stream);
    cudaMemcpyAsync(points, input, numPoints * sizeof(half), cudaMemcpyHostToDevice, stream);
    ```

3. Use half precision for data type.
   half-precision has many coding problems. It is only supported by GPU with compute capability 5.0 or above. For 5.0 and 6.0, the advantage of half precision is not obvious. 

   When compiling, you need to add `-arch=sm_xx`, where xx is the compute capability of your GPU. xx should be larger than 50.

   Use `__half2float` and `__float2half` to convert between half and float.

   Half precision can not be put in cin and cout. 

   Use `__hadd`, `__hmul`, `__hdiv`, `hsqrt`, etc, to do half precision arithmetic operations. You can't use `+`, `*` directly.

4. Downsample the point cloud.
   
   In the beginning, I just get the first N points for inference. In this way, I find that the performance is not good when N is small. 

   So I begin to downsample the point cloud by uniform sampling. I find that with points with close index are also close in space. So I just randomly choose a point in a fixed interval for downsampling.

   This method is quite simple and effective. The performance is much better. The inference time is reduced from 5.6 seconds to 0.18 seconds, almost 30 times.

   ```C++
    for (int i = 0; i < len; i++) {
        std::string name = dataset_names[i];
        DataSet dataset = file.openDataSet(name + "/points");
        DataSpace dataspace = dataset.getSpace();

        // 获取数据集的维度
        hsize_t dims[2];
        dataspace.getSimpleExtentDims(dims, NULL);
        int num_points = dims[0] * dims[1];
        // 读取数据
        cudaMallocHost((void**)&temp_points, num_points * sizeof(float));
        cudaMallocHost((void**)&(*list_of_points)[i], (num_points / INTERVAL + 3) * sizeof(half));
        lengths.push_back(num_points / INTERVAL + 3);
        dataset.read(temp_points, PredType::NATIVE_FLOAT);
        for (int j = 0; j < num_points; j += (INTERVAL * 3)) {
            (*list_of_points)[i][j / INTERVAL] = __float2half(temp_points[j]);
            (*list_of_points)[i][j / INTERVAL + 1] = __float2half(temp_points[j + 1]);
            (*list_of_points)[i][j / INTERVAL + 2] = __float2half(temp_points[j + 2]);
        }
        cudaFreeHost(temp_points);


        // 读取标签`
        Attribute label_attr = file.openGroup(name).openAttribute("label");
        int label;
        label_attr.read(PredType::NATIVE_INT, &label);

        // 存储标签
        list_of_labels.push_back(label);
    }
   ```

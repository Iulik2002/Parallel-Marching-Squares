# Homework 1

In this assignment, I parallelized the Marching Squares algorithm using Pthreads to accelerate the process across multiple threads. For parallelization, I used the standard formula, which helps me divide the iterations into sections, and each thread will operate in parallel on its section:

int start = ID * (double)N / P;
int end = min((ID + 1) * (double)N / P, N);
Thus, the functions rescale_image, sample_grid, and march were modified to allow their execution on separate threads.

rescale_image
From this function, I removed all allocations and moved them to main, and I added a condition to understand whether it makes sense to scale the image or not, and for each iteration, I applied the formula mentioned above.

sample_grid
Similarly, I removed allocations from here, putting them in main, and applied the formula to allow execution on separate threads.

march
Here I had nothing to do, besides applying the formula.

The most challenging part was that the formula uses an ID for each section (index) and the number of threads, which I couldn't easily obtain within the function, so I created two structures. The first one contains all the necessary data for a thread, and the second one, an ID and a pointer to the first structure:

typedef struct {
    ppm_image *image;
    ppm_image *scaled_image;
    unsigned char **grid;
    ppm_image **contour_map;
    int step_x;
    int step_y;
    int num_threads;
    pthread_barrier_t barrier;
} ThreadData;

typedef struct {
    int id;
    ThreadData* thdata;
} Id;
In the main function, I initialized all the structures used, after which I called create and join for the specified number of threads. When calling the pthread_create function, I used a function called myfunc as a parameter. In this function, I call all the functions that I parallelized, and between functions, I place a barrier, to stop the threads that need input that has not been provided by other threads.

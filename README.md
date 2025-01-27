# PDC
"Parallel Max Finder using OpenMP"
#c code

#include <stdio.h>
#include <pthread.h>
#include <stdlib.h>

#define SIZE 10000
#define NUM_THREADS 4

int array[SIZE];
int max_values[NUM_THREADS]; // Store local maximums for each thread

void* find_max(void* arg) {
    int thread_id = *(int*)arg;
    int start = thread_id * (SIZE / NUM_THREADS);
    int end = (thread_id + 1) * (SIZE / NUM_THREADS);

    int local_max = array[start];
    for (int i = start + 1; i < end; i++) {
        if (array[i] > local_max) {
            local_max = array[i];
        }
    }
    max_values[thread_id] = local_max;
    pthread_exit(NULL);
}

int main() {
    pthread_t threads[NUM_THREADS];
    int thread_ids[NUM_THREADS];

   //Initialize the array with values
    
    for (int i = 0; i < SIZE; i++) {
        array[i] = i;
    }

    // Create threads to find local maximums
    for (int i = 0; i < NUM_THREADS; i++) {
        thread_ids[i] = i;
        if (pthread_create(&threads[i], NULL, find_max, &thread_ids[i]) != 0) {
            perror("Error creating thread");
            exit(EXIT_FAILURE);
        }
    }

    // Wait for threads to finish
    for (int i = 0; i < NUM_THREADS; i++) {
        if (pthread_join(threads[i], NULL) != 0) {
            perror("Error joining thread");
            exit(EXIT_FAILURE);
        }
    }

    // Find the global maximum from local maximums
    int max_value = max_values[0];
    for (int i = 1; i < NUM_THREADS; i++) {
        if (max_values[i] > max_value) {
            max_value = max_values[i];
        }
    }

    printf("Maximum value in the array: %d\n", max_value);
    return 0;
}

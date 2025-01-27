# PDC
"Parallel Max Finder using OpenMP"
#c code

#include <stdio.h>
#include <omp.h>

#define SIZE 10000

int main() {
    int array[SIZE];
    int max_value = 0;
    for (int i = 0; i < SIZE; i++) {
        array[i] = i; 
    }
    #pragma omp parallel
    {
        int local_max = 0;
        #pragma omp for
        for (int i = 0; i < SIZE; i++) {
            if (array[i] > local_max) {
                local_max = array[i];
        }
        }
        #pragma omp critical
        {
            if (local_max > max_value) {
                max_value = local_max;
            }
        }
    }
    printf("Maximum value in the array: %d\n", max_value);
    return 0;
}

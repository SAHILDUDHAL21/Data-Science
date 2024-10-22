### execve.c


```python
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <string.h>

void sort(int *array, int n) {
    for (int i = 0; i < n - 1; i++)
        for (int j = 0; j < n - i - 1; j++)
            if (array[j] > array[j + 1]) {
                int temp = array[j];
                array[j] = array[j + 1];
                array[j + 1] = temp;
            }
}

int main(int argc, char *argv[]) {
    if (argc < 3) return 1;

    int n = argc - 2;
    int *array = malloc(n * sizeof(int));
    for (int i = 0; i < n; i++) array[i] = atoi(argv[i + 2]);

    sort(array, n);
    
    char *args[n + 2];
    args[0] = "./execve1";
    args[1] = argv[1];
    for (int i = 0; i < n; i++) {
        args[i + 2] = malloc(12);
        sprintf(args[i + 2], "%d", array[i]);
    }
    args[n + 2] = NULL;

    if (fork() == 0) {
        execve(args[0], args, NULL);
        perror("execve failed");
        exit(1);
    }

    for (int i = 2; i < n + 2; i++) free(args[i]);
    free(array);
    return 0;
}

```

##### execve1.c    
#####              gcc -o execve1 execve1.c
#####               gcc execve.c
#####            ./a.out 5 6 3 2 7 8 2


```python
#include <stdio.h>
#include <stdlib.h>

int binary_search(int *array, int n, int item) {
    int left = 0, right = n - 1;
    while (left <= right) {
        int mid = left + (right - left) / 2;
        if (array[mid] == item) return mid;
        if (array[mid] < item) left = mid + 1;
        else right = mid - 1;
    }
    return -1;
}

int main(int argc, char *argv[]) {
    if (argc < 3) return 1;

    int item = atoi(argv[1]);
    int n = argc - 2;
    int *array = malloc(n * sizeof(int));
    for (int i = 0; i < n; i++) array[i] = atoi(argv[i + 2]);

    int result = binary_search(array, n, item);
    printf("Item %d %s\n", item, result != -1 ? "found" : "not found");

    free(array);
    return 0;
}

```

##### Fifo


```python
//FIFO Page Replacement Algorithm
#include <stdio.h>
int main() {
    int i, j, n, frames, pages[30], temp[10], flag1, flag2, pos = 0, faults = 0;

    printf("Enter number of pages: ");
    scanf("%d", &n);

    printf("Enter the page reference string: ");
    for (i = 0; i < n; i++) {
        scanf("%d", &pages[i]);
    }

    printf("Enter number of frames: ");
    scanf("%d", &frames);

    for (i = 0; i < frames; i++) {
temp[i] = -1;
    }

    printf("\nPage Reference String  |  Frames\n");

    for (i = 0; i < n; i++) {
        flag1 = flag2 = 0;
        for (j = 0; j < frames; j++) {
            if (temp[j] == pages[i]) {
                flag1 = flag2 = 1;
                break;
            }
 }

        if (flag1 == 0) {

            for (j = 0; j < frames; j++) {
                if (temp[j] == -1) {
                    temp[j] = pages[i];
                    flag2 = 1;
                    faults++;
                    break;
                }
            }
        }

        if (flag2 == 0) {
            temp[pos] = pages[i];
            pos = (pos + 1) % frames;
            faults++;
        }

        printf("\n%2d\t\t\t", pages[i]);
        for (j = 0; j < frames; j++) {
            if (temp[j] != -1)
                printf("%2d ", temp[j]);
            else
                printf("- ");
        }
    }

    printf("\n\nTotal Page Faults: %d\n", faults);
    return 0;
}

```

##### LRU


```python
#include <stdio.h>

int findLRU(int time[], int frames) 
{
    int min = time[0], pos = 0;
    for (int i = 1; i < frames; i++)
    {
        if (time[i] < min) 
        { 
            min = time[i]; 
            pos = i; 
        }
    }
    return pos;
}

int main() 
{
    int n, frames, pages[30], temp[10] = {-1}, time[10] = {0}, faults = 0, hits = 0, counter = 0;

    printf("Enter number of pages: ");
    scanf("%d", &n);
    printf("Enter the page reference string: ");
    for (int i = 0; i < n; i++)
    {
        scanf("%d", &pages[i]);
    }
    printf("Enter number of frames: ");
    scanf("%d", &frames);

    printf("\nPage Reference | Frames\n");
    for (int i = 0; i < n; i++) 
    {
        int flag = 0;
        for (int j = 0; j < frames; j++) 
        {
            if (temp[j] == pages[i]) 
            {
                time[j] = ++counter; 
                hits++; 
                flag = 1; 
                break; 
            }
            if (temp[j] == -1) 
            {
                temp[j] = pages[i];
                time[j] = ++counter; 
                faults++; 
                flag = 1; 
                break; 
            }
        }
        if (!flag) 
        {
            temp[findLRU(time, frames)] = pages[i];
            time[findLRU(time, frames)] = ++counter; 
            faults++;
        }
        printf("%2d            | ", pages[i]);
        for (int j = 0; j < frames; j++)
        {
            printf("%2s ", temp[j] != -1 ? (temp[j] < 10 ? " " : "") : "-");
        }
        printf("\n");
    }

    printf("\nTotal Page Faults: %d\n", faults);
    printf("Total Page Hits: %d\n", hits);
    return 0;
}
```


```python
optimal
```


```python
#include <stdio.h>
int findOptimal(int pages[], int temp[], int n, int current, int frames) {
    int pos = -1, farthest = current;
    for (int i = 0; i < frames; i++) {
        int j;
        for (j = current; j < n; j++) {
            if (temp[i] == pages[j]) {
                if (j > farthest) {
                    farthest = j;
                    pos = i;
                }
                break;
            }
        }
        if (j == n) // If a page is not going to be used again
            return i;
    }
    return (pos == -1) ? 0 : pos; // If all pages will be used, replace the farthest one
}
int main() {
    int i, j, n, frames, pages[30], temp[10], flag1, flag2, faults = 0;

    printf("Enter number of pages: ");
    scanf("%d", &n);
    printf("Enter the page reference string: ");
    for (i = 0; i < n; i++) {
        scanf("%d", &pages[i]);
    }
    printf("Enter number of frames: ");
    scanf("%d", &frames);

    // Initialize frames to -1 (indicating empty)
    for (i = 0; i < frames; i++) {
        temp[i] = -1;
    }
    printf("\nPage Reference String  |  Frames\n");

    for (i = 0; i < n; i++) {
        flag1 = flag2 = 0;
        // Check if the page is already in one of the frames
        for (j = 0; j < frames; j++) {
            if (temp[j] == pages[i]) {
                flag1 = flag2 = 1;
                break;
            }
        }
        // If the page is not in the frame, find where to place it
        if (flag1 == 0) {
            // Find an empty frame to place the page
            for (j = 0; j < frames; j++) {
                if (temp[j] == -1) {
                    temp[j] = pages[i];
                    flag2 = 1;
                    faults++;
                    break;
                }
            }
        }
        // If no empty frame, use the optimal page replacement strategy
        if (flag2 == 0) {
            int pos = findOptimal(pages, temp, n, i + 1, frames);
            temp[pos] = pages[i];
            faults++;
        }
        // Display the current status of frames
        printf("\n%2d\t\t\t", pages[i]);
        for (j = 0; j < frames; j++) {
            if (temp[j] != -1)
                printf("%2d ", temp[j]);
            else
                printf("- ");
        }
    }
    printf("\n\nTotal Page Faults: %d\n", faults);
    return 0;
}

```


```python
dir.c
gcc dir.c
./a.out     run kelayar myshell$ asa yel
myshell$  list f dir-name        -- display files in dir or folder
myshell$  list n dir-name        -- display no of files
myshell$  list i dir-name        -- display filename and file size in bits 
```


```python
#include <stdio.h> 
#include <stdlib.h> 
#include <unistd.h> 
#include <dirent.h> 
#include <string.h>     
#include <sys/wait.h> 

char *buff, *t1, *t2, *t3, ch; 
int pid; 

void list(char t2, char *t3) 
{ 
    DIR *dir; 
    struct dirent *entry; 
    int cnt = 0; 
    dir = opendir(t3);
    if (dir == NULL) 
    { 
        printf("Directory %s not found\n", t3);
        return; 
    } 
    switch (t2) 
    { 
        case 'f':
            while ((entry = readdir(dir)) != NULL) 
            { 
                printf("%s\n", entry->d_name);
            } 
            break; 
        case 'n':
            while ((entry = readdir(dir)) != NULL) 
                cnt++; 
            printf("Total No of Entries: %d\n", cnt);
            break; 
        case 'i':
            while ((entry = readdir(dir)) != NULL) 
            { 
                printf("\n%s\t %d", entry->d_name, entry->d_ino);
            } 
            break; 
        default: 
            printf("Invalid argument\n"); 
    } 
    closedir(dir);
}

int main()
{ 
    while (1) 
    { 
        printf("myshell$ ");
        fflush(stdin);
        t1 = (char *)malloc(80);
        t2 = (char *)malloc(80);
        t3 = (char *)malloc(80);
        buff = (char *)malloc(80);
        fgets(buff, 80, stdin);
        sscanf(buff, "%s %s %s", t1, t2, t3);
        
        if (strcmp(t1, "pause") == 0) 
            exit(0);
        else if (strcmp(t1, "list") == 0) 
            list(t2[0], t3);
        else
        { 
            pid = fork();
            if (pid < 0) 
                printf("Child process is not created\n"); 
            else if (pid == 0) 
            { 
                execlp(t1, t1, NULL); 
                perror("execlp failed");
                exit(1); 
            } 
            else
            { 
                wait(NULL);
            } 
        } 
        
        free(t1); 
        free(t2);
        free(t3);
        free(buff);
    } 
}
```

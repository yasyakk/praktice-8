# Практична робота №8  

## Системні виклики в UNIX/POSIX (файлові операції, fork(), qsort(), write(), read(), lseek() тощо 

## Варіант 9 


## Мета роботи

Дослідити роботу системних викликів UNIX/POSIX для файлових операцій, керування процесами та сортування даних, а також експериментально проаналізувати їхню поведінку в різних умовах виконання.

## Завдання 8.1 

Чи може write() записати менше байтів?

Так. write() може повернути менше, ніж nbytes
Причини:
- запис у pipe / socket
- заповнений буфер
- сигнал перервав виклик
- нестача ресурсів


### Створення:

nano task8_1.c

### Код програми:

#include <stdio.h>
#include <unistd.h>
#include <fcntl.h>
#include <string.h>
#include <sys/resource.h>

int main() {
  
    struct rlimit rl;
    rl.rlim_cur = 1024;
    rl.rlim_max = 1024;
    setrlimit(RLIMIT_FSIZE, &rl);

    int fd = open("testfile.bin", O_CREAT | O_WRONLY, 0644);

    char buffer[4096];
    memset(buffer, 'A', sizeof(buffer));

    ssize_t count = write(fd, buffer, sizeof(buffer));

    printf("Requested: %ld bytes\n", sizeof(buffer));
    printf("Written: %ld bytes\n", count);

    close(fd);
    return 0;
}

### Компіляція:

gcc task8_1.c -o task8_1

./task8_1

## Завдання 8.2

### Створення:

nano task8_2.c

### Код:

#include <stdio.h>

#include <unistd.h>

#include <fcntl.h>

int main() {

int fd = open("data.bin", O_CREAT | 

O_RDWR, 0644);

unsigned char data[] = {4,5,2,2,3,3,7,9,1,5};

write(fd, data, sizeof(data));

lseek(fd, 3, SEEK_SET);

unsigned char buffer[4];

read(fd, buffer, 4);

printf("Buffer contains: ");

for(int i = 0; i < 4; i++)

printf("%d ", buffer[i]);

printf("\n");

close(fd);

return 0;

}

### Запуск:

gcc task8_2.c -o task8_2

./task8_2

## Завдання 8.3 — Найгірші дані для 
qsort()

### Створення:

nano task8_3.c

### Код:

#include <stdio.h>
#include <stdlib.h>
#include <time.h>

int compare(const void *a, const void *b) {
    int x = *(int*)a;
    int y = *(int*)b;
    return x - y;
}

void test_array(int *arr, int n, const char *name) {
    clock_t start = clock();
    qsort(arr, n, sizeof(int), compare);
    clock_t end = clock();

    double time_spent = (double)(end - start) / CLOCKS_PER_SEC;
    printf("%s time: %f seconds\n", name, time_spent);
}

int main() {
    int n = 100000;

    int *sorted = malloc(n * sizeof(int));
    int *reverse = malloc(n * sizeof(int));
    int *same = malloc(n * sizeof(int));

    for(int i = 0; i < n; i++) {
        sorted[i] = i;
        reverse[i] = n - i;
        same[i] = 1;
    }

    test_array(sorted, n, "Sorted");
    test_array(reverse, n, "Reverse");
    test_array(same, n, "Same values");

    free(sorted);
    free(reverse);
    free(same);
}

### Запуск:

gcc task8_3.c -o task8_3

./task8_3

### правильність qsort:

for(int i = 1; i < n; i++) {
    if(arr[i-1] > arr[i]) {
        printf("Sort error!\n");
        break;
    }
}

## Завдання 8.4 — fork()

### Створення:

nano task8_4.c

### Код:

#include <stdio.h>
#include <unistd.h>

int main() {
    int pid;
    pid = fork();
    printf("%d\n", pid);
    return 0;
}

### Запуск програми: 

gcc task8_4.c -o task8_4

./task8_4

## Варіант 9 — fork() у циклі

### Створення:

nano variant9.c

### Код:

#include <stdio.h>
#include <unistd.h>

int main() {
    for(int i = 0; i < 4; i++) {
        fork();
        printf("Process PID: %d, iteration: %d\n", getpid(), i);
        sleep(1);
    }
    return 0;
}

### Запуск:

gcc variant9.c -o variant9

./variant9

## Висновок:

Під час виконання практичної роботи було досліджено особливості системних викликів POSIX. Встановлено, що write() може записувати менше байтів через обмеження буферів. Досліджено роботу lseek() та позиціювання у файлі. Експериментально визначено найгірші вхідні дані для qsort. Вивчено поведінку fork() та експоненційне зростання кількості процесів при виклику в циклі.


## Результати роботи програми:

Усі скріншоти виконання практичної роботи знаходяться в папці:

[screenshots](./screenshots)

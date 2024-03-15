# tema1

Tema 1 APD

In aceasta tema am paralelizat algoritmului Marching Squares, folosind Pthreads pentru a accelera procesul pe
mai multe fire de execuție. Pentru paralelizare am folosit formula standardad, care ma ajuta sa impart
iteratiile in sectiuni, iar fiecare thread va opera in paralel pe sectiunea sa: 

int start = ID * (double)N / P;
int end = min((ID + 1) * (double)N / P, N);

Astfel funcțiile `rescale_image`, `sample_grid`, și `march` au fost modificate pentru a permite execuția lor
pe fire de execuție separate.

`rescale_image`
Din aceasta functie am scos toate alocarile si le-am mutat in `main` si am pus o conditie pentru a intelege
daca are sens sau nu de a scala imaginea si pentru fiecare iteratie am aplicat formula mentionata mai sus.

`sample_grid`
La fel am scos si de aici alocarile, punandu-le in `main` si am aplicat formula pentru a permite executia pe
fire separate

`march`
Aici nu am avut nimic de facut, pe langa aplicarea formulei

Cea mai grea parte, consta in faptul ca formula foloseste cate un ID pentru fiecare sectiune (index) si
numarul de threads, ceea ce eu nu le puteam obtine simplu in functie, si pentru asta mi-am facut doua
structuri. Prima contine toate datele necesare pentru un thread, iar a doua, un ID si un pointer catre prima
structura:

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

In functia `main` am intializat toate structurile folosite dupa care am apelat create si join pentru numarul 
specificat de threads. La apelul functiei `pthread_create` am folosit ca parametru o functie numita `myfunc`.
In aceasta functie apelez toate functiile care le-am paralelizat, iar intre functii plasez cate o bariera, 
pentru oprirea firelor ce au nevoie de un input care nu a fost realizat de alte fire. 

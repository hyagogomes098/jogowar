# jogowar
trabalho da faculdade
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

typedef struct Territorio {
    char nome[30];
    int exercitos;
    struct Territorio* vizinhos;
    int numVizinhos;
} Territorio;

/* ---------------------------------------------------------
   FUNÇÕES DE TERRITÓRIO
   --------------------------------------------------------- */

// Cria um território dinamicamente
Territorio* criarTerritorio(const char* nome, int exercitos) {
    Territorio* t = (Territorio*)calloc(1, sizeof(Territorio));
    if (!t) {
        printf("Erro ao alocar território!\n");
        exit(1);
    }

    strcpy(t->nome, nome);
    t->exercitos = exercitos;
    t->numVizinhos = 0;
    t->vizinhos = NULL;

    return t;
}

// Adiciona um território vizinho
void adicionarVizinho(Territorio* t, Territorio* vizinho) {
    if (!t || !vizinho) return;

    t->vizinhos = (Territorio*)realloc(t->vizinhos, (t->numVizinhos + 1) * sizeof(Territorio));
    if (!t->vizinhos) {
        printf("Erro de memória ao adicionar vizinho!\n");
        exit(1);
    }

    t->vizinhos[t->numVizinhos] = *vizinho;
    t->numVizinhos++;
}

// Exibe informações
void mostrarTerritorio(const Territorio* t) {
    if (!t) return;

    printf("\n--- Território: %s ---\n", t->nome);
    printf("Exércitos: %d\n", t->exercitos);
    printf("Vizinhos (%d): ", t->numVizinhos);

    for (int i = 0; i < t->numVizinhos; i++) {
        printf("%s", t->vizinhos[i].nome);
        if (i < t->numVizinhos - 1) printf(", ");
    }
    printf("\n");
}

/* ---------------------------------------------------------
   LÓGICA DE ATAQUE
   --------------------------------------------------------- */

int atacar(Territorio* atacante, Territorio* defensor, int tropas) {
    if (!atacante || !defensor) return 0;

    if (tropas <= 0 || tropas > atacante->exercitos - 1) {
        printf("Número inválido de tropas!\n");
        return 0;
    }

    printf("\n%s está atacando %s com %d tropas!\n",
           atacante->nome, defensor->nome, tropas);

    // Mecânica simples: quem tem mais soldados vence
    if (tropas > defensor->exercitos) {
        printf("Vitória! %s conquistou %s!\n", atacante->nome, defensor->nome);
        defensor->exercitos = tropas - defensor->exercitos;
        atacante->exercitos -= tropas;
        return 1;
    } else {
        printf("Ataque falhou! Tropas derrotadas.\n");
        atacante->exercitos -= tropas;
        return 0;
    }
}

/* ---------------------------------------------------------
   LIBERAÇÃO DE MEMÓRIA
   --------------------------------------------------------- */

void destruirTerritorio(Territorio* t) {
    if (!t) return;
    if (t->vizinhos) free(t->vizinhos);
    free(t);
}

/* ---------------------------------------------------------
   PROGRAMA PRINCIPAL
   --------------------------------------------------------- */

int main() {
    Territorio* brasil = criarTerritorio("Brasil", 10);
    Territorio* argentina = criarTerritorio("Argentina", 7);
    Territorio* chile = criarTerritorio("Chile", 5);

    adicionarVizinho(brasil, argentina);
    adicionarVizinho(brasil, chile);
    adicionarVizinho(argentina, chile);

    mostrarTerritorio(brasil);
    mostrarTerritorio(argentina);
    mostrarTerritorio(chile);

    atacar(brasil, argentina, 6);

    mostrarTerritorio(brasil);
    mostrarTerritorio(argentina);

    destruirTerritorio(brasil);
    destruirTerritorio(argentina);
    destruirTerritorio(chile);

    return 0;
}

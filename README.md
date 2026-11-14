#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define MAX_ITENS 1000
#define MAX_NM 100

typedef struct {
    char nome[MAX_NM];
    int quantidade;
} Item;
void limparString(char texto[]) {
    texto[strcspn(texto, "\n")] = '\0';
}
int carregarEstoque(Item estoque[]) {
    FILE *arquivo = fopen("inventory.txt", "r");

    int total = 0;

    if (arquivo == NULL) {
        return 0;
    }
    while (total < MAX_ITENS &&
           fgets(estoque[total].nome, MAX_NM, arquivo)) {

        limparString(estoque[total].nome);

        if (fscanf(arquivo, "%d",
                   &estoque[total].quantidade) != 1) {
            break;
        }
        int c;
        while ((c = fgetc(arquivo)) != '\n' && c != EOF);

        total++;
    }
    fclose(arquivo);
    return total;
}
void salvarEstoque(Item estoque[], int total) {
    FILE *arquivo = fopen("inventory.txt", "w");

    if (arquivo == NULL) {
        printf("Erro ao abrir o arquivo!\n");
        return;
    }

    for (int i = 0; i < total; i++) {
        fprintf(arquivo, "%s\n%d\n",
                estoque[i].nome,
                estoque[i].quantidade);
    }

    fclose(arquivo);}
void adicionarItem() {
    Item estoque[MAX_ITENS];

    int total = carregarEstoque(estoque);
    char nome[MAX_NM];
    int quantidade;
    printf("Digite o nome do item: ");
    fgets(nome, MAX_NM, stdin);
    limparString(nome);

    if (strlen(nome) == 0) {
        printf("Nome invalido!\n");
        return;
    }
    printf("Digite a quantidade: ");
    if (scanf("%d", &quantidade) != 1 || quantidade <= 0) {
        printf("Quantidade invalida!\n");
        while (getchar() != '\n');
        return;
    }
    while (getchar() != '\n');
    for (int i = 0; i < total; i++) {
        if (strcmp(estoque[i].nome, nome) == 0) {
            estoque[i].quantidade += quantidade;
            salvarEstoque(estoque, total);
            printf("Item adicionado com sucesso!\n");
            return;
        }  }
    if (total >= MAX_ITENS) {
        printf("Limite de itens atingido!\n");
        return;
    }
    strcpy(estoque[total].nome, nome);
    estoque[total].quantidade = quantidade;

    total++;

    salvarEstoque(estoque, total);

    printf("Item adicionado com sucesso!\n");
}
void removerItem() {
    Item estoque[MAX_ITENS];

    int total = carregarEstoque(estoque);
    char nome[MAX_NM];
    int quantidade;
    int encontrado = 0;

    printf("Digite o nome do item: ");
    fgets(nome, MAX_NM, stdin);
    limparString(nome);

    printf("Digite a quantidade a ser removida: ");

    if (scanf("%d", &quantidade) != 1 || quantidade <= 0) {
        printf("Quantidade invalida!\n");
        while (getchar() != '\n');
        return;
    }
    while (getchar() != '\n');

    for (int i = 0; i < total; i++) {

        if (strcmp(estoque[i].nome, nome) == 0) {

            encontrado = 1;

            if (quantidade > estoque[i].quantidade) {
                printf("Estoque insuficiente. Quantidade disponivel: %d\n",
                       estoque[i].quantidade);
                return;
            }
            estoque[i].quantidade -= quantidade;

            if (estoque[i].quantidade == 0) {
                for (int j = i; j < total - 1; j++) {
                    estoque[j] = estoque[j + 1];
                }
                total--;
                printf("Item removido do estoque!\n");
            } else {
                printf("Quantidade atualizada com sucesso!\n");
            }
            salvarEstoque(estoque, total);
            return;
        }
    }

    if (!encontrado) {
        printf("Item nao encontrado.\n");
    }
}
void listarEstoque() {
    Item estoque[MAX_ITENS];

    int total = carregarEstoque(estoque);

    if (total == 0) {
        printf("O estoque esta vazio.\n");
        return;
    }

    printf("\n====================================\n");
    printf("           Estoque Atual\n");
    printf("====================================\n");
    for (int i = 0; i < total; i++) {
        printf("Nome: %s\n", estoque[i].nome);
        printf("Quantidade: %d\n\n",
               estoque[i].quantidade);
    }}
int main() {
    int opcao;
    char continuar;
    do {

        printf("\n====================================\n");
        printf("          Controle de Estoque\n");
        printf("====================================\n");

        printf("1. Adicionar Item\n");
        printf("2. Remover Item\n");
        printf("3. Listar Estoque\n");
        printf("4. Sair\n");
        printf("Opcao: ");
        if (scanf("%d", &opcao) != 1) {
            printf("Opcao invalida! Tente novamente.\n");

            while (getchar() != '\n');

            continue;
        }
        while (getchar() != '\n');

        if (opcao < 1 || opcao > 4) {
            printf("Opcao invalida! Tente novamente.\n");
            continue;
        }
        switch (opcao) {

            case 1:
                adicionarItem();
                break;

            case 2:
                removerItem();
                break;

            case 3:
                listarEstoque();
                break;

            case 4:
                printf("Obrigado por usar o Controle de Estoque! Ate a proxima.\n");
                return 0;
        }
        printf("\nDeseja realizar outra operacao (s/n): ");
        scanf(" %c", &continuar);
        while (getchar() != '\n');
        if (continuar != 's' && continuar != 'S') {
            printf("Obrigado por usar o Controle de Estoque! Ate a proxima.\n");
            break;
        }
    } while (1);
    return 0;
}

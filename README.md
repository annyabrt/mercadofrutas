# mercadofrutas

// Trabalho por Annya Barreto, turma terça noite, matrícula: 202202235431
// professor: Niltemberg
// Estrutura de Dados


#include <stdio.h>
#include <stdlib.h>
#include <string.h>

// Definindo a estrutura da fruta
typedef struct Fruta {
    int codigo;
    char nome[50];
    int quantidade;
    float preco;
    struct Fruta* proximo;
} Fruta;

// Função para criar uma nova fruta
Fruta* criarFruta(int codigo, char* nome, int quantidade, float preco) {
    Fruta* novaFruta = (Fruta*)malloc(sizeof(Fruta));
    novaFruta->codigo = codigo;
    strcpy(novaFruta->nome, nome);
    novaFruta->quantidade = quantidade;
    novaFruta->preco = preco;
    novaFruta->proximo = NULL;
    return novaFruta;
}

// Definindo a estrutura da fila
typedef struct Fila {
    Fruta* frente;
    Fruta* tras;
} Fila;

// Função para criar uma nova fila
Fila* criarFila() {
    Fila* fila = (Fila*)malloc(sizeof(Fila));
    fila->frente = NULL;
    fila->tras = NULL;
    return fila;
}

// Função para verificar se a fila está vazia
int filaVazia(Fila* fila) {
    return (fila->frente == NULL);
}

// Função para enfileirar uma fruta
void enfileirar(Fila* fila, Fruta* fruta) {
    if (filaVazia(fila)) {
        fila->frente = fruta;
    } else {
        fila->tras->proximo = fruta;
    }
    fila->tras = fruta;
}

// Função para verificar se um código já existe
int codigoExiste(Fila* fila, int codigo) {
    Fruta* atual = fila->frente;
    while (atual != NULL) {
        if (atual->codigo == codigo) return 1;
        atual = atual->proximo;
    }
    return 0;
}

// Função para cadastrar uma nova fruta
void cadastrarFruta(Fila* fila) {
    int codigo;
    char nome[50];
    int quantidade;
    float preco;
    
    printf("Digite o código da fruta: ");
    scanf("%d", &codigo);
    if (codigoExiste(fila, codigo)) {
        printf("Código de fruta já existe!\n");
        return;
    }

    printf("Digite o nome da fruta: ");
    scanf("%s", nome);
    printf("Digite a quantidade da fruta: ");
    scanf("%d", &quantidade);
    printf("Digite o preço da fruta: ");
    scanf("%f", &preco);
    
    enfileirar(fila, criarFruta(codigo, nome, quantidade, preco));
    printf("Fruta cadastrada com sucesso!\n");
}

// Função para listar todas as frutas
void listarFrutas(Fila* fila) {
    if (filaVazia(fila)) {
        printf("Nenhuma fruta cadastrada.\n");
        return;
    }
    Fruta* atual = fila->frente;
    while (atual != NULL) {
        printf("Código: %d, Nome: %s, Quantidade: %d, Preço: %.2f\n", atual->codigo, atual->nome, atual->quantidade, atual->preco);
        atual = atual->proximo;
    }
}

void buscarFruta(Fila* fila) {
    if (filaVazia(fila)) {
        printf("Nenhuma fruta cadastrada.\n");
        return;
    }
    int codigo;
    printf("Digite o código da fruta: ");
    scanf("%d", &codigo);
    
    Fruta* atual = fila->frente;
    while (atual != NULL) {
        if (atual->codigo == codigo) {
            printf("Código: %d, Nome: %s, Quantidade: %d, Preço: %.2f\n", atual->codigo, atual->nome, atual->quantidade, atual->preco);
            return;
        }
        atual = atual->proximo;
    }
    printf("Fruta não encontrada.\n");

}

// Função para buscar a fruta
void buscarFruta(Fila* fila) {
    if (filaVazia(fila)) {
        printf("Nenhuma fruta cadastrada.\n");
        return;
    }
    int codigo;
    printf("Digite o código da fruta: ");
    scanf("%d", &codigo);
    
    Fruta* atual = fila->frente;
    while (atual != NULL) {
        if (atual->codigo == codigo) {
            printf("Código: %d, Nome: %s, Quantidade: %d, Preço: %.2f\n", atual->codigo, atual->nome, atual->quantidade, atual->preco);
            return;
        }
        atual = atual->proximo;
    }
    printf("Fruta não encontrada.\n");
}


// Função para alterar os dados de uma fruta
void alterarFruta(Fila* fila) {
    int codigo;
    printf("Digite o código da fruta: ");
    scanf("%d", &codigo);
    
    Fruta* fruta = buscarFruta(fila, codigo);
    if (fruta == NULL) {
        printf("Fruta não encontrada.\n");
        return;
    }
    
    printf("Digite o novo nome da fruta: ");
    scanf("%s", fruta->nome);
    printf("Digite a nova quantidade da fruta: ");
    scanf("%d", &fruta->quantidade);
    printf("Digite o novo preço da fruta: ");
    scanf("%f", &fruta->preco);
    printf("Fruta alterada com sucesso!\n");
}

// Função para excluir uma fruta pelo código
void excluirFruta(Fila* fila) {
    int codigo;
    printf("Digite o código da fruta: ");
    scanf("%d", &codigo);
    
    Fruta* atual = fila->frente;
    Fruta* anterior = NULL;
    
    while (atual != NULL && atual->codigo != codigo) {
        anterior = atual;
        atual = atual->proximo;
    }
    
    if (atual == NULL) {
        printf("Fruta não encontrada.\n");
        return;
    }
    
    if (anterior == NULL) {
        fila->frente = atual->proximo;
    } else {
        anterior->proximo = atual->proximo;
    }
    
    if (atual->proximo == NULL) {
        fila->tras = anterior;
    }
    
    free(atual);
    printf("Fruta excluída com sucesso!\n");
}

// Função para vender uma fruta
void venderFruta(Fila* fila) {
    int codigo, quantidade;
    printf("Digite o código da fruta: ");
    scanf("%d", &codigo);
    printf("Digite a quantidade a ser vendida: ");
    scanf("%d", &quantidade);
    
    Fruta* fruta = buscarFruta(fila, codigo);
    if (fruta == NULL) {
        printf("Fruta não encontrada.\n");
        return;
    }
    
    if (fruta->quantidade < quantidade) {
        printf("Quantidade insuficiente em estoque.\n");
        return;
    }
    
    fruta->quantidade -= quantidade;
    FILE* arquivo = fopen("vendas.txt", "a");
    if (arquivo == NULL) {
        printf("Erro ao abrir arquivo de vendas.\n");
        return;
    }
    fprintf(arquivo, "Código: %d, Nome: %s, Quantidade Vendida: %d, Preço Total: %.2f\n", fruta->codigo, fruta->nome, quantidade, quantidade * fruta->preco);
    fclose(arquivo);
    printf("Venda realizada com sucesso!\n");
}

// Função para exibir o menu
void exibirMenu() {
    printf("\nMenu:\n");
    printf("1. Cadastrar fruta\n");
    printf("2. Listar frutas\n");
    printf("3. Buscar fruta\n");
    printf("4. Alterar fruta\n");
    printf("5. Excluir fruta\n");
    printf("6. Vender fruta\n");
    printf("7. Sair\n");
    printf("Escolha uma opção: ");
}

int main() {
    Fila* fila = criarFila();
    int opcao;
    
    do {
        exibirMenu();
        scanf("%d", &opcao);
        switch (opcao) {
            case 1:
                cadastrarFruta(fila);
                break;
            case 2:
                listarFrutas(fila);
                break;
            case 3:
                buscarFruta(fila);
                break;
            case 4:
                alterarFruta(fila);
                break;
            case 5:
                excluirFruta(fila);
                break;
            case 6:
                venderFruta(fila);
                break;
            case 7:
                printf("Encerrado.\n");
                break;
            default:
                printf("Opção inválida!\n");
        }
    } while (opcao != 6);
    
    return 0;
}

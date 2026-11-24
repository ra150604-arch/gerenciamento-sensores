# gerenciamento-sensores


#include <stdio.h>
#include <stdlib.h>

typedef struct Sensor {
    int codigo;
    float temperatura;
    struct Sensor *proximo;
} Sensor;

int Menu() {
    int opcao;
    printf("\n ====MENU CADASTRO DE SENSORES====\n");
    printf("\t 1 - Adicionar sensor no começo da lista\n");
    printf("\t 2 - Adicionar sensor no final da lista\n");
    printf("\t 3 - Listar todos os sensores cadastrados\n");
    printf("\t 4 - Buscar um sensor por seu código\n");
    printf("\t 5 - Remover um sensor pelo código\n");
    printf("\t 6 - Calcular a temperatura média dos sensores\n");
    printf("\t 7 - Contar quantos sensores possuem temperatura superior a um valor informado\n");
    printf("\t 0 - Encerrar o programa liberando toda a memória alocada\n");
    printf("\t Escolha uma opção: ");
    scanf("%d", &opcao);
    return opcao;
}

// Função auxiliar para alocar e inicializar um novo nó
Sensor* criarSensor(int codigo, float temperatura) {
    Sensor *novo = (Sensor*) malloc(sizeof(Sensor));
    if (novo == NULL) {
        printf("Erro ao alocar memória!\n");
        return NULL;
    }
    novo->codigo = codigo;
    novo->temperatura = temperatura;
    novo->proximo = NULL;
    return novo;
}

// 1 - Adicionar sensor no começo da lista
Sensor* inserirInicio(Sensor *inicio, int codigo, float temperatura) {
    Sensor *novo = criarSensor(codigo, temperatura);
    if (novo == NULL) return inicio;

    novo->proximo = inicio;
    printf("Sensor %d adicionado no início com sucesso!\n", codigo);
    return novo;
}

// 2 - Adicionar sensor no final da lista
Sensor* inserirFim(Sensor *inicio, int codigo, float temperatura) {
    Sensor *novo = criarSensor(codigo, temperatura);
    if (novo == NULL) return inicio;

    if (inicio == NULL) {
        printf("Sensor %d adicionado com sucesso!\n", codigo);
        return novo;
    }

    Sensor *atual = inicio;
    while (atual->proximo != NULL) {
        atual = atual->proximo;
    }
    atual->proximo = novo;

    printf("Sensor %d adicionado no final com sucesso!\n", codigo);
    return inicio;
}

// 3 - Listar todos os sensores cadastrados
void listarSensores(Sensor *inicio) {
    if (inicio == NULL) {
        printf("Nenhum sensor cadastrado.\n");
        return;
    }

    printf("\n--- Lista de Sensores ---\n");
    Sensor *atual = inicio;
    while (atual != NULL) {
        printf("Código: %d | Temperatura: %.2f °C\n", atual->codigo, atual->temperatura);
        atual = atual->proximo;
    }
}

// 4 - Buscar um sensor por seu código
Sensor* buscarSensor(Sensor *inicio, int codigo) {
    Sensor *atual = inicio;
    while (atual != NULL) {
        if (atual->codigo == codigo) {
            return atual;
        }
        atual = atual->proximo;
    }
    return NULL;
}

// 5 - Remover um sensor pelo código
Sensor* removerSensor(Sensor *inicio, int codigo) {
    if (inicio == NULL) {
        printf("Lista vazia. Nenhum sensor para remover.\n");
        return NULL;
    }

    Sensor *atual = inicio;
    Sensor *anterior = NULL;

    while (atual != NULL && atual->codigo != codigo) {
        anterior = atual;
        atual = atual->proximo;
    }

    if (atual == NULL) {
        printf("Sensor com código %d não encontrado.\n", codigo);
        return inicio;
    }

    if (anterior == NULL) {
        // Remoção do primeiro elemento da lista
        inicio = atual->proximo;
    } else {
        // Remoção no meio ou no final da lista
        anterior->proximo = atual->proximo;
    }

    free(atual);
    printf("Sensor %d removido com sucesso!\n", codigo);
    return inicio;
}

// 6 - Calcular a temperatura média dos sensores
void calcularMedia(Sensor *inicio) {
    if (inicio == NULL) {
        printf("Lista vazia. Não é possível calcular a média.\n");
        return;
    }

    float soma = 0.0f;
    int quantidade = 0;
    Sensor *atual = inicio;

    while (atual != NULL) {
        soma += atual->temperatura;
        quantidade++;
        atual = atual->proximo;
    }

    printf("Temperatura média (%d sensor(es)): %.2f °C\n", quantidade, soma / quantidade);
}

// 7 - Contar sensores com temperatura superior a um valor informado
int contarTemperaturaSuperior(Sensor *inicio, float limite) {
    int contador = 0;
    Sensor *atual = inicio;

    while (atual != NULL) {
        if (atual->temperatura > limite) {
            contador++;
        }
        atual = atual->proximo;
    }

    return contador;
}

// 0 - Liberar toda a memória alocada
Sensor* liberarLista(Sensor *inicio) {
    Sensor *atual = inicio;
    while (atual != NULL) {
        Sensor *proximo = atual->proximo;
        free(atual);
        atual = proximo;
    }
    return NULL;
}

int main() {
    Sensor *lista = NULL;
    int opcao, codigo;
    float temperatura, limite;

    do {
        opcao = Menu();

        switch (opcao) {
            case 1:
                printf("Informe o código do sensor: ");
                scanf("%d", &codigo);
                printf("Informe a temperatura: ");
                scanf("%f", &temperatura);
                lista = inserirInicio(lista, codigo, temperatura);
                break;

            case 2:
                printf("Informe o código do sensor: ");
                scanf("%d", &codigo);
                printf("Informe a temperatura: ");
                scanf("%f", &temperatura);
                lista = inserirFim(lista, codigo, temperatura);
                break;

            case 3:
                listarSensores(lista);
                break;

            case 4: {
                printf("Informe o código do sensor para busca: ");
                scanf("%d", &codigo);
                Sensor *encontrado = buscarSensor(lista, codigo);
                if (encontrado != NULL) {
                    printf("Sensor encontrado -> Código: %d | Temperatura: %.2f °C\n",
                           encontrado->codigo, encontrado->temperatura);
                } else {
                    printf("Sensor com código %d não encontrado.\n", codigo);
                }
                break;
            }

            case 5:
                printf("Informe o código do sensor para remover: ");
                scanf("%d", &codigo);
                lista = removerSensor(lista, codigo);
                break;

            case 6:
                calcularMedia(lista);
                break;

            case 7:
                printf("Informe a temperatura de referência: ");
                scanf("%f", &limite);
                printf("Quantidade de sensores acima de %.2f °C: %d\n",
                       limite, contarTemperaturaSuperior(lista, limite));
                break;

            case 0:
                lista = liberarLista(lista);
                printf("Memória liberada. Encerrando o programa...\n");
                break;

            default:
                printf("Opção inválida! Tente novamente.\n");
        }
    } while (opcao != 0);

    return 0;
}

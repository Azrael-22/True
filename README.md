# True
Estrutura do Sistema
1. Classe Reserva
A classe Reserva conterá os detalhes da reserva, como nome do hotel, número do quarto, data de check-in e data de check-out.

2. Classe SistemaReservas
A classe SistemaReservas será responsável por:

Implementar a função hash original.
Inserir novas reservas.
Buscar reservas.
Remover reservas.
Carregar dados de reservas a partir de um arquivo.
Exibir a configuração do hashmap.
Estrutura do Arquivo de Entrada
O formato do arquivo de entrada será:


10
ABC123 HotelA 101 2024-06-10 2024-06-15
DEF456 HotelB 102 2024-06-11 2024-06-16
...
A primeira linha indica o número de posições da hash, e cada linha subsequente representa uma reserva a ser inserida.

Implementação em Java
Classe Reserva
java
Copiar código
public class Reserva {
    private String nomeHotel;
    private int numeroQuarto;
    private String dataCheckIn;
    private String dataCheckOut;

    public Reserva(String nomeHotel, int numeroQuarto, String dataCheckIn, String dataCheckOut) {
        this.nomeHotel = nomeHotel;
        this.numeroQuarto = numeroQuarto;
        this.dataCheckIn = dataCheckIn;
        this.dataCheckOut = dataCheckOut;
    }

    @Override
    public String toString() {
        return "Hotel: " + nomeHotel + ", Quarto: " + numeroQuarto + ", Check-In: " + dataCheckIn + ", Check-Out: " + dataCheckOut;
    }
}
Classe SistemaReservas

import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;
import java.util.Arrays;

public class SistemaReservas {
    private int tamanho;
    private Object[][] hashmap;

    public SistemaReservas(int tamanho) {
        this.tamanho = tamanho;
        this.hashmap = new Object[tamanho][2];
    }

    private int funcaoHash(String chave) {
        return chave.chars().reduce(0, (acc, charValue) -> acc + charValue) % tamanho;
    }

    public void inserirReserva(String codigoReserva, Reserva reserva) {
        int indice = funcaoHash(codigoReserva);
        while (hashmap[indice][0] != null) {
            if (hashmap[indice][0].equals(codigoReserva)) {
                break;
            }
            indice = (indice + 1) % tamanho;
        }
        hashmap[indice][0] = codigoReserva;
        hashmap[indice][1] = reserva;
    }

    public Reserva buscarReserva(String codigoReserva) {
        int indice = funcaoHash(codigoReserva);
        while (hashmap[indice][0] != null) {
            if (hashmap[indice][0].equals(codigoReserva)) {
                return (Reserva) hashmap[indice][1];
            }
            indice = (indice + 1) % tamanho;
        }
        return null;
    }

    public boolean removerReserva(String codigoReserva) {
        int indice = funcaoHash(codigoReserva);
        while (hashmap[indice][0] != null) {
            if (hashmap[indice][0].equals(codigoReserva)) {
                hashmap[indice][0] = null;
                hashmap[indice][1] = null;
                rehash(indice);
                return true;
            }
            indice = (indice + 1) % tamanho;
        }
        return false;
    }

    private void rehash(int indice) {
        int nextIndice = (indice + 1) % tamanho;
        while (hashmap[nextIndice][0] != null) {
            String nextCodigoReserva = (String) hashmap[nextIndice][0];
            Reserva nextReserva = (Reserva) hashmap[nextIndice][1];
            hashmap[nextIndice][0] = null;
            hashmap[nextIndice][1] = null;
            inserirReserva(nextCodigoReserva, nextReserva);
            nextIndice = (nextIndice + 1) % tamanho;
        }
    }

    public void carregarDados(String arquivo) throws IOException {
        BufferedReader br = new BufferedReader(new FileReader(arquivo));
        int tamanho = Integer.parseInt(br.readLine().trim());
        SistemaReservas sistema = new SistemaReservas(tamanho);

        String linha;
        while ((linha = br.readLine()) != null) {
            String[] dados = linha.split(" ");
            String codigoReserva = dados[0];
            String nomeHotel = dados[1];
            int numeroQuarto = Integer.parseInt(dados[2]);
            String dataCheckIn = dados[3];
            String dataCheckOut = dados[4];

            Reserva reserva = new Reserva(nomeHotel, numeroQuarto, dataCheckIn, dataCheckOut);
            sistema.inserirReserva(codigoReserva, reserva);
        }

        br.close();
    }

    public void exibirHashmap() {
        for (int i = 0; i < tamanho; i++) {
            if (hashmap[i][0] != null) {
                System.out.println(i + ": " + hashmap[i][0] + " -> " + hashmap[i][1]);
            } else {
                System.out.println(i + ": null");
            }
        }
    }

    public static void main(String[] args) {
        try {
            SistemaReservas sistema = new SistemaReservas(10);
            sistema.carregarDados("reservas.txt");
            sistema.exibirHashmap();

            Reserva r = sistema.buscarReserva("ABC123");
            System.out.println(r);

            sistema.removerReserva("ABC123");
            sistema.exibirHashmap();

        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
 

# Sistema de Reservas de Hotéis

## Descrição
Este sistema de reservas de hotéis utiliza um hashmap para armazenar, buscar e remover reservas de forma eficiente. Cada reserva é mapeada a um código de identificação único, que serve como chave no hashmap.

## Estrutura dos Dados da Reserva
Cada reserva contém as seguintes informações:
- Nome do Hotel
- Número do Quarto
- Data de Check-In
- Data de Check-Out

## Função Hash
A função hash utilizada para gerar as chaves do hashmap é definida como a soma dos valores ASCII dos caracteres da chave:
```java
private int funcaoHash(String chave) {
    return chave.chars().reduce(0, (acc, charValue) -> acc + charValue) % tamanho;
}
Tratamento de Colisões
As colisões são tratadas utilizando encadeamento linear. Quando uma colisão ocorre, o sistema procura o próximo índice disponível na tabela hash.

Funcionalidades
Inserir Reserva: Adiciona uma nova reserva ao hashmap.
Buscar Reserva: Recupera os detalhes de uma reserva usando o código da reserva.
Remover Reserva: Remove uma reserva do hashmap após o check-out ou cancelamento.
Carregar Dados: Carrega reservas a partir de um arquivo.
Exibir Hashmap: Exibe a configuração atual do hashmap.
Exemplo de Uso

public static void main(String[] args) {
    try {
        SistemaReservas sistema = new SistemaReservas(10);
        sistema.carregarDados("reservas.txt");
        sistema.exibirHashmap();

        Reserva r = sistema.buscarReserva("ABC123");
        System.out.println(r);

        sistema.removerReserva("ABC123");
        sistema.exibirHashmap();

    } catch (IOException e) {
        e.printStackTrace();
    }
}
Contribuição
Este projeto foi desenvolvido por um grupo de até 6 pessoas, conforme permitido. A colaboração incluiu o desenvolvimento da função hash original, a implementação do sistema de reservas, e a documentação detalhada no Readme.md.


### Conclusão

Este sistema de reservas de hotéis em Java atende aos requisitos especificados, oferecendo inserção, busca 

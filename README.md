# Árvore B
# Introdução 

Uma *Árvore B* é uma estrutura de dados auto-balanceada que permite armazenar grandes quantidades de dados de forma eficiente. Ela é amplamente utilizada em bancos de dados e sistemas de arquivos devido à sua capacidade de reduzir a profundidade da árvore e minimizar o tempo de busca.

## Características da Árvore B

- Cada nó pode conter múltiplas chaves.
- Cada nó pode ter múltiplos filhos.
- Todos os nós folha estão no mesmo nível.
- A árvore está sempre balanceada.
  
![image](https://github.com/user-attachments/assets/2439a3f3-f104-40b9-813d-a268781efe04)

## Operações Básicas

### Busca
A busca em uma Árvore B funciona de forma similar à busca binária. O algoritmo percorre as chaves do nó atual até encontrar a chave desejada ou decidir em qual subárvore continuar a busca.

### Inserção
1. A chave é inserida em um nó folha.
2. Se o nó estiver cheio, ele será dividido.
3. A chave do meio sobe para o pai, repetindo o processo se necessário.

### Remoção
1. Se a chave estiver em um nó folha, basta removê-la.
2. Se a chave estiver em um nó interno, substitua-a pela chave imediatamente maior.
3. Se a remoção causar um nó a ficar abaixo do limite mínimo de chaves, será necessário realizar uma redistribuição ou fusão com um nó vizinho.

## Exemplo em JavaScript

Exemplo de Árvore B em JavaScript:

```javascript
class BTreeNode {
    constructor(order, leaf = false) {
        this.order = order;
        this.leaf = leaf;
        this.keys = [];
        this.children = [];
    }
}

class BTree {
    constructor(order) {
        this.root = new BTreeNode(order, true);
        this.order = order;
    }

    search(node, key) {
        let i = 0;
        while (i < node.keys.length && key > node.keys[i]) {
            i++;
        }
        if (i < node.keys.length && key === node.keys[i]) {
            return true;
        }
        if (node.leaf) {
            return false;
        }
        return this.search(node.children[i], key);
    }

    insert(key) {
        let root = this.root;
        if (root.keys.length === (2 * this.order) - 1) {
            let newNode = new BTreeNode(this.order, false);
            this.root = newNode;
            newNode.children.push(root);
            this.splitChild(newNode, 0, root);
            this.insertNonFull(newNode, key);
        } else {
            this.insertNonFull(root, key);
        }
    }

    insertNonFull(node, key) {
        let i = node.keys.length - 1;
        if (node.leaf) {
            node.keys.push(null);
            while (i >= 0 && key < node.keys[i]) {
                node.keys[i + 1] = node.keys[i];
                i--;
            }
            node.keys[i + 1] = key;
        } else {
            while (i >= 0 && key < node.keys[i]) {
                i--;
            }
            i++;
            if (node.children[i].keys.length === (2 * this.order) - 1) {
                this.splitChild(node, i, node.children[i]);
                if (key > node.keys[i]) {
                    i++;
                }
            }
            this.insertNonFull(node.children[i], key);
        }
    }

    splitChild(parent, index, child) {
        let newNode = new BTreeNode(this.order, child.leaf);
        parent.keys.splice(index, 0, child.keys[this.order - 1]);
        parent.children.splice(index + 1, 0, newNode);
        newNode.keys = child.keys.splice(this.order);
        if (!child.leaf) {
            newNode.children = child.children.splice(this.order);
        }
    }
}

// Exemplo de uso
let tree = new BTree(3);
tree.insert(10);
tree.insert(20);
tree.insert(5);
tree.insert(6);
tree.insert(12);
console.log(tree.search(tree.root, 10)); // true

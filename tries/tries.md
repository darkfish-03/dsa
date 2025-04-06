
```
class TrieNode {
private:
    TrieNode* links[26];
    bool isEnd;
public:
    bool containsKey(char ch) {
        return links[ch - 'a'] != nullptr;
    }

    void put(char ch, TrieNode* node) {
        links[ch - 'a'] = node;
    }

    TrieNode* get(char ch) {
        return links[ch - 'a'];
    }

    void setEnd() {
        isEnd = true;
    }

    bool isEndOfWord() {
        return isEnd;
    }
};

class Trie {
private:
    TrieNode* root;    
public:
    Trie() {
        root = new TrieNode();
    }
    
    void insert(string word) {
        TrieNode* node = root;
        for(int i=0; i<word.size(); i++) {
            char ch = word[i];
            if(!node->containsKey(ch)) {
                node->put(ch, new TrieNode());
            }
            node = node->get(ch);
        }
        node->setEnd();
    }
    
    bool search(string word) {
        TrieNode* node = root;
       for(int i=0; i<word.size(); i++) {
            char ch = word[i];
            if(!node->containsKey(ch)) {
                return false;
            }
            node = node->get(ch);
        } 
        return node->isEndOfWord();
    }
    
    bool startsWith(string prefix) {
        TrieNode* node = root;
        for(int i=0; i<prefix.size(); i++) {
            char ch = prefix[i];
            if(!node->containsKey(ch)) {
                return false;
            }
            node = node->get(ch);
        } 
        return true;
    }
};

```

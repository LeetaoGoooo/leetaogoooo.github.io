---
title: Replace Words
tags: [Leetcode]

date: 2017-09-19
published: true
hideInList: false
feature: 
isTop: false
---







In English, we have a concept called <code>root</code>, which can be followed by some other words to form another longer word - let's call this word <code>successor</code>. For example, the root <code>an</code>, followed by <code>other</code>, which can form another word <code>another</code>.

Now, given a dictionary consisting of many roots and a sentence. You need to replace all the <code>successor</code> in the sentence with the <code>root</code> forming it. If a <code>successor</code> has many <code>roots</code> can form it, replace it with the root with the shortest length.

You need to output the sentence after the replacement.

**Example 1:**

```shell
Input: dict = ["cat", "bat", "rat"]
sentence = "the cattle was rattled by the battery"
Output: "the cat was rat by the bat"
```

**Note:**
1. The input will only have lower-case letters.
2. 1 <= dict words number <= 1000
3. 1 <= sentence words number <= 1000
4. 1 <= root length <= 100
5. 1 <= sentence words length <= 1000


**Solution**

将字符串(sentence)拆分为数组(sentence_arr),然后遍历 dic,判断数组元素前导字符串是否包含dic中的元素,如果包含则替换


Python

Prefix-hash

```python
class Solution(object):
    def replaceWords(self, dict, sentence):
        """
        :type dict: List[str]
        :type sentence: str
        :rtype: str
        """
        res = []
        sentence_arr = sentence.split(" ")
        for i in range(len(sentence_arr)):
            for item in dict:
                if sentence_arr[i].startswith(item):
                    sentence_arr[i] = item
        return " ".join(sentence_arr)
```

Trie

利用dict构建字典树,然后遍历 sentence 中的元素, 在 Trie 中搜索

[参考代码1](https://discuss.leetcode.com/topic/96826/python-straightforward-with-explanation-prefix-hash-trie-solutions):
```python
class Solution(object):
    def replaceWords(self, roots, sentence):
        _trie = lambda: collections.defaultdict(_trie)
        trie = _trie()
        END = True
        for root in roots:
            cur = trie
            for letter in root:
                cur = cur[letter]
            cur[END] = root

        def replace(word):
            cur = trie
            for letter in word:
                if letter not in cur: break
                cur = cur[letter]
                if END in cur:
                    return cur[END]
            return word

        return " ".join(map(replace, sentence.split()))
```

[参考代码2](https://discuss.leetcode.com/topic/96833/using-trie-python)

```python
class Solution(object):
    def replaceWords(self, roots, sentence):
        """
        :type dict: List[str]
        :type sentence: str
        :rtype: str
        """

        class TrieNode(object):
            def __init__(self):
                self.word = False
                self.children = dict()

        class Trie(object):
            def __init__(self):
                self.root = TrieNode()

            def addWord(self, word):
                if word:
                    cur = self.root
                    for c in word:
                        if c not in cur.children:
                            cur.children[c] = TrieNode()
                        cur = cur.children[c]
                    cur.word = True

            def getRoot(self, word):
                ans = ""
                cur = self.root
                for c in word:
                    if c in cur.children:
                        ans += c
                        cur = cur.children[c]
                        if cur.word == True:  # found smallest root!
                            return ans
                    else:
                        break
                return word

        trie = Trie()
        for word in roots:
            trie.addWord(word)

        ans = []
        for word in sentence.split():
            ans.append(trie.getRoot(word))
        return ' '.join(ans)
```
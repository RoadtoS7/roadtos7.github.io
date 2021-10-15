---
title: "Algorithm05 - DoublyLinkedList(2중 연결리스트)"
data: "2020-04-17 22:58:17 -400"
categories: Algorithm
background: "/img/classic_blue.jpeg"
---

## DoublyLinkedList(2중 연결리스트)
```
class Node:
  def __init__(self, data):
    self.data = data
    self.right = None
    self.left = None
    
class DoublyLinkedList(object):
  def __init__(self):
    self.head = None
    
  def append(self, node):
    if self.head:
      cur = self.head
      while cur.right:
        cur = cur.right
      cur.right = node
      node.left = cur
    else:
      self.head = node
    
  def insertNodeAtIndex(self, idx, node):
    cur = self.head
    prev = None
    curIdx = 0
    
    if idx == 0:
      nextn = self.head
      self.head = node
      node.right = nextn
      nextn.left = node
    else:
      while curIdx < idx:
        if cur:
          prev = cur
          cur = cur.right
          curIdx += 1
        else:
          break
      if curIdx == idx:
        prev.right = node
        node.left = prev
        node.right = cur
        if cur:
          cur.left = node
      else:
        return -1
        
  def getDataIndex(self, data):
    cur = self.head
    curIdx = 0
    
      while cur.right:
        if cur.data == data:
          return curIdx
        cur = cur.right
        curIdx += 1
      return -1
      
  def insertNodeAtData(self, data, node):
    idx = self.getDataIndex(data)
    if idx >= 0:
      self.inserNodeAtIndex(idx)
    else:
      return -1
      
  def deleteAtIndex(self, idx):
    cur = self.head
    prev = None
    curIdx = 0
    
    if idx == 0:
      if self.head:
        self.head = cur.right
      else:
        return -1
    else:
      while curIdx < idx:
        if cur.right:
          prev = cur
          cur = cur.right
          curIdx += 1
        else:
          break
      if curIdx == idx:
        prev.right = cur.right
        if cur.right:
          cur.right.left = prev
      else:
        return -1
        
  def print(self):
    cur = self.head
    string = ""
    
    while cur:
      string += str(cur.data)
      if cur.right:
        string += "<->"
      cur = cur.right
    print(string)
    
    
if __name__ == "__main__":
  dl = DoublyLinkedList()
    dl.append(Node(1))
    dl.append(Node(2))
    dl.append(Node(3))
    dl.append(Node(4))
    dl.append(Node(6))
    dl.print()
    dl.insertNodeAtIndex(4, Node(7))
    dl.print()
    dl.insertNodeAtIndex(6, Node(5))
    dl.print()
    dl.deleteAtIndex(6)
    dl.print()
```

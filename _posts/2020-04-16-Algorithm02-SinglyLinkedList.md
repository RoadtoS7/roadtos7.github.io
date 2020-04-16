---
title: "DataStructure01 - SinglyLinkedList(단일 연결리스트)"
date: "2020-04-16 20:37:17 -400"
categories: Algorithm
---

## SinglyLinkedList(단일 연결리스트)
```
class Node:
  def __init__(self, data):
    self.data = data  
    self.next = None
    
class SinglyLinkedList(object):
  def __init__(self):
    self.head = None
    self.count = 0
    
  def append(self, node):
    if self.head:
      cur = self.head
      while cur.next:
        cur = cur.next
      cur.next = node
    else:
      self.head = node
    
      
  def getDataAtIndex(self, idx):
    cur = self.head
    curIdx = 0
    
    while curIdx < idx:
      if cur:
        cur = cur.next
        curIdx += 1
      else:
        break
    if curIdx == idx:
      return cur.data
    else:
      return -1
      
  def getDataIndex(self, data):
    cur = self.head
    curIdx = 0
    while cur:
      if cur.data == data:
         return curIdx
       cur.next = cur
       curIdx += 1
    return -1
      
  def insertNodeAtIndex(self, idx, node):
    prev = None
    cur = self.head
    curIdx = 0
    
    if idx == 0:
      nextn = cur
      cur = node
      node.next = nextn
    else:
      while curIdx < idx:
        if cur:
          prev = cur
          cur = cur.next
          curIdx += 1
        else:
          break
      if curIdx == idx:
        prev.next = node
        node.next = cur
      else:
        return -1
        
  def insertNodeAtData(self, data, node):     
     idx = self.getDataIndex(data)
     if idx > 0:        
       self.insertNodeAtIndex(Node(idx))
     else:
       return -1

  def deleteAtIndex(self, idx):
    prev = None
    cur = self.head
    curIdx = idx

    if idx == 0:
      self.head = cur.next    
    else:
      while curIdx < idx:
        if cur:
          prev = cur
          cur = cur.next
          curIdx += 1
        else:
          break
      if curIdx == idx:
        prev.next = cur.next
      else:
        return -1

    def clear(self):
      self.head = None

    def print(self):
      string = ""
      cur = self.head
      while cur:
        string += str(cur.data)
        if cur.next:
          string += "=>"
        cur = cur.next
      print(string)

        
if __name__ == "__main__":
  sl = SinglyLinkedList()
  sl.append(Node(1))
  sl.append(Node(2))
  sl.append(Node(3))
  sl.append(Node(5))
  sl.insertNodeAtIndex(3, Node(4))
  sl.print()

          
      
    
```

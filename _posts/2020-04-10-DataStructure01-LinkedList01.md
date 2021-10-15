---
title: "자료구조: Linked List 01 - Singl Linked List"
data: "2020-04-10 19:44:17 -400"
background: "/img/classic_blue.jpeg"
categories: DataStructure
---

## 자료구조: Linked List 01 - Singl Linked List
### Singly linked list
Singly linked list는 데이터와 주소를 다음 노드의 주소를 담고 있는 노드들이 **한줄**로 연결되어있는 방식의 자료구조입니다.

따라서 노드는 다음과 같이 이루어져 있습니다.  
```
class Node:
  self.data = data
  self.next = None
```

Linked List는 Array처럼 선형 자료 구조 이지만, Array는 물리적으로도 논리적으로도 데이터가 연속적으로 저장되어있습니다.  
반면에, Linked List는 메모리상에 물리적으로도 연속적으로 존재하지 않습니다.  

Array의 삽입과 삭제의 시간 복잡도는 O(n)입니다. 
왜냐하면 배열에서 데이터가 물리적으로 연속적으로 존재하기 대문에 데이터를 옮기는 작업이 필요하기 때문입니다.  

반면에 LinkedList는 다음노드의 주소를 저장하여 다음 노드를 가리킴으로써 연결을 형성하기 때문에,  
삽입, 삭제시 Node에 담김 다음 노드의 주소만 바꾸어주면 되어 데이터를 옮기는 작업이 필요하지 않습니다.  
따라서 삽입, 삭제에서는 LinkedList가 Array보다 효율적입니다.  

하지만 특정 원소를 탐색하는 경우에는 LinkedList는 반드시 첫번째 노드부터 차례대로 찾아나가야 하기 때문에 시간복잡도가 O(n)입니다.  
결과적으로 탐색의 경우에는 Array에 비해서 LinkedList가 덜 효율적입니다. 

정리하자면 LinkedList의 장점과 단점은 다음과 같습니다. 
#### LinkedList의 장점, 단점
1. 장점
- 길이를 동적으로 조작가능하다.
- 데이터의 삽입과 삭제가 가능하다.

2. 단점
- 데이터 탐색이 어렵다. 즉, 임의의 노드에 바로 접근할 수 없다.
- 다음 노드의 위치를 저장하기 위한 추가적인 공간이 필요하다.
- Cache Locality를 활용해 근접 데이터를 사전에 캐시에 저장하기 어렵다.
- 거꾸로 탐색하기 어렵다.

#### 코드
```
# Node 클래스 
class Node(object):
  self.data = data
  self.Next = None

# Singliy Linked List 클래스
class SingliyLinkedList(object):
  def __init__(self):
    self.head = None
    self.count = 0
    
  # append
  def append(self, node):
    if self.head == None
      self.head = node
    else:
      cur = self.head
      while cur.next != None:
        cur = cur.next
      cur.next = node
      
  def getDataIndex(self,data):
      curn = self.head
      idx = 0
      while curn:
        if curn.data == data:
          reutrn idx
        curn = curn.next
        idx += 1
      return -1
      
  def insertNodeAtIndex(self,idx,node):
        curn = self.head
        prevn = Node
        cur_i = 0
        
        if idx == 0
          if self.head:
            nextn = self.head
            self.head = node
            node.next - nextn
          else:
            self.head = node
        else:
          while curn_i < idx:
            if curn:
              prevn = curn
              curn = curn.next
            else:
              break
            cur_i += 1
          if cur_i == idx:
            node.next = curn
            prevn.next = node
          else:
            return -1
            
        
            
  
```


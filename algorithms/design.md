# Design

## 1. LRU Cache

* LC [146.LRU Cache](https://leetcode.com/problems/lru-cache/)

{% tabs %}
{% tab title="OrderdDict(FAST)" %}
```python
# O(1) ===========================================================
# IDEA: the last guy in OrderdDict() is the least recently used one
from collections import OrderedDict

class LRUCache:

   def __init__(self, capacity: int):
        self.size = capacity
        self.cache = OrderedDict()
    
    # O(1)
    def get(self, key: int) -> int:
        if key not in self.cache:
            return -1
        else:
            # O(1), since OrderedDict is a dict + double linked list
            self.cache.move_to_end(key)  # Gotta keep this pair fresh, move to end of OrderedDict
            return self.cache[key]
    # O(1)
    def put(self, key: int, value: int) -> None:
        if key not in self.cache:
            if len(self.cache) >= self.size:
                # O(1)
                self.cache.popitem(last=False) # last=True, LIFO; last=False, FIFO. We want to remove in FIFO fashion. 
        else:
            # O(1)
            self.cache.move_to_end(key) # Gotta keep this pair fresh, move to end of OrderedDict
        self.cache[key] = value
        
'''
LRUCache lRUCache = new LRUCache(2);
lRUCache.put(1, 1); // cache is {1=1}
lRUCache.put(2, 2); // cache is {1=1, 2=2}
lRUCache.get(1);    // return 1
lRUCache.put(3, 3); // LRU key was 2, evicts key 2, cache is {1=1, 3=3}
lRUCache.get(2);    // returns -1 (not found)
lRUCache.put(4, 4); // LRU key was 1, evicts key 1, cache is {4=4, 3=3}
lRUCache.get(1);    // return -1 (not found)
lRUCache.get(3);    // return 3
lRUCache.get(4);    // return 4
'''
```
{% endtab %}

{% tab title="deque(SLOW)" %}
```python
# O(N) ======================================
# JHANDU APPROACH --------------------------- XXXX
from collections import deque

class LRUCache:

    def __init__(self, capacity: int):
        self.size = capacity
        self.deque = deque()    # key->val
        '''
                deque
        left ================= right
least_recently_used      most_recently_used
        '''
    # O(N)
    def _find(self, key: int) -> int:
		""" Linearly scans the deque for a specified key. Returns -1 if it does not exist, returns the index of the deque if it exists"""
        for i in range(len(self.deque)):
            self.deque[i][0] == key:
                return i
        return -1
    
    # O(N)
    def get(self, key: int) -> int:
        idx = self._find(key)
        if idx == -1:
            return -1
        else:
            k, v = self.deque[idx]
            del self.deque[idx] # We have to put this pair at the end of the queue so, we have to delete it first
            self.deque.append((k, v)) # And now, we put it at the end of the queue. So, the most viewed ones will be always at the end and be saved from popping when new capacity got hit.
            return v
                
    # O(N)
    def put(self, key: int, value: int) -> None:
        idx = self._find(key)
        if idx == -1:
            if len(self.deque) >= self.size:
                self.deque.popleft()
            self.deque.append((key,value))
        else:
            del self.deque[idx]
            self.deque.append((key, value))
```
{% endtab %}
{% endtabs %}

## 2. LFU Cache ✅😅

* LC [460.LFU Cache](https://leetcode.com/problems/lfu-cache/) | [video.py](https://www.youtube.com/watch?v=Jn4mbZVkeik\&ab\_channel=babybear4812)

{% tabs %}
{% tab title="LFU" %}
```python
'''
# TC:  get: O(1), put: O(1) ==============================================
node: obj(val, freq)
IDEA: keep 2 dictionaries:

1. nodeKeys ={key -> (val, freq)} or {key -> node}  # data on key-val pair & how many times its repeated yet
2. nodeCounts = {freq -> OrderedDict({key ->node})}

* Also keep track of least freq yet (minFreq)

freq:1 => {1:(1,1),2:(2,1)}  # the 'LRU' order(needed during tie)
freq:2 => 
freq:3 => {3:(1,3), 4:(5,3)}  # the 'LRU' order(needed during tie)

'''

from collections import defaultdict,OrderedDict

class Node:
    def __init__(self, val, freq):
        self.val = val
        self.freq = freq
    
class LFUCache(object):
    def __init__(self, capacity):
        self.capacity = capacity
        self.nodeKeys = {}                          # key -> node
        self.nodeCounts = defaultdict(OrderedDict)  # freq -> (key,node)
        self.minFreq = None
        
    def put(self, key, value):
        if not self.capacity:   #input sanity check
            return 
        
        # 1. if in cache-> update the value
        # 2. if not in cache:
            # 2.1 if we're at capacity
                # remove
            # 2.2 else:
                # add item
                
        if key in self.nodeKeys:
            self.nodeKeys[key].val = value
            self.get(key) # get increases the freq internally
            return
        
        if len(self.nodeKeys) == self.capacity:
            # pop the first(head) item in orderdDict
            lfu_key, _ = self.nodeCounts[self.minFreq].popitem(last=False) 
            del self.nodeKeys[lfu_key] 
            
        newNode = Node(value,1)
        #update both dicts for this new entry & least_freq_yet
        self.nodeKeys[key] = newNode
        self.nodeCounts[1][key] = newNode
        self.minFreq = 1
        return
        
    def get(self, key)->int:
        if key not in self.nodeKeys:
            return -1
        
        # update its frequency bucket
        node = self.nodeKeys[key]
        del self.nodeCounts[node.freq][key]
        
        node.freq += 1
        self.nodeCounts[node.freq][key] = node
        
        # update minFreq : if this bucket is empty => our minFreq has increase
        if not self.nodeCounts[self.minFreq]:
            self.minFreq += 1

        return node.val
```
{% endtab %}
{% endtabs %}

## 2. Movie Rental System

* LC [1912. Design Movie Rental System](https://leetcode.com/problems/design-movie-rental-system/)
* Sol: [https://leetcode.com/problems/design-movie-rental-system/discuss/1298440/Python-SortedList-solution-explained](https://leetcode.com/problems/design-movie-rental-system/discuss/1298440/Python-SortedList-solution-explained)

## 3. D.S. with O(1) insert|delete|delRandom

{% tabs %}
{% tab title="w/o concurr" %}
```python
'''
Design data structure to get/insert/delete an element. 
It should also support DeleteRandom method. 
All the methods should be O(1) | 

SOLN:
=================================================================================
I used Map(Index in the list, element) and List(element).

Add(Element):

add to the end of the list. O(1)
add to the map. O(1)
Delete(Element):

Get the index(i) of the element from the list. O(1)
Remove Element from the map. O(1)
Move the last element (Last_Element) from the list to index i and delete the last element. O(1)
Update the index of the Last_Element in map. O(1)
DeleteRandom:

Get a random_number between 0 and list.size()-1. O(1)
Call Delete(list.get(random_number))
=================================================================================

[PART#2: handle concurrency]
'''
import random

class Sharper:
    def __init__(self):
        self.mapper = dict()    # val -> index in the list
        self.lister = []         
    
    # O(1)
    def get(self, val:int) ->int:
        idx = self.mapper[val]      # O(1)
        return self.lister[idx]     # O(1)
    
    # O(1)
    def insert(self, val:int) -> int:
        curr_size = len(self.lister)
        self.mapper[val] = curr_size        # add to map
        self.lister.append(val)             # append to list

    # O(1)
    def delete(self, val:int) -> None:
        # get index of val from map
        idx = self.mapper[val]  
        print(f'\t idx = {idx}')
        print(f'\t self.lister = {self.lister}')
        # swap ele@index with last ele in list
        self.lister[idx] , self.lister[-1] = self.lister[-1], self.lister[idx]
        
        print(f'\t self.lister = {self.lister}')
        # now delete last ele from list & remove it from map too
        self.mapper.pop(self.lister[-1]) 
        self.lister.pop(-1)
    
    # O(1)
    def deleteRandom(self) ->None:
        rand_idx = random.randint(0,len(self.lister) -1)
        self.delete(self.lister[rand_idx])
        
ds = Sharper()
ds.insert(1)
ds.insert(4)
ds.insert(2)
ds.insert(3)
print(ds.lister)
print(ds.mapper)
ds.insert(5)
print(ds.lister)
print(ds.mapper)
print(ds.get(1))
print(ds.lister)
print(ds.mapper)
ds.delete(4)
print(ds.lister)
print(ds.mapper)
ds.deleteRandom()
print(ds.lister)
print(ds.mapper)
```
{% endtab %}

{% tab title="Second Tab" %}

{% endtab %}
{% endtabs %}

## From LC:

* [x] LC [427. Construct Quad Tree](https://leetcode.com/problems/construct-quad-tree/)
* [x] LC [1600. Throne Inheritance](https://leetcode.com/problems/throne-inheritance/)
* [x] LC [295. Find median from a data stream](https://leetcode.com/problems/find-median-from-data-stream/)
* [x] LC [480. Sliding Window Median](https://leetcode.com/problems/sliding-window-median/)
* [ ] LC 348. Design Tic-Tac-Toe
* [ ] LC 534. Design Tiny URL
* [ ] LC 535.Encode and Decode TinyURL
* [ ] LC 346. Moving Average from Data Stream
* [ ] LC 281. Zigzag Iterator
* [ ] LC 381. Insert Delete GetRandom O(1) - Duplicates allowed
* [ ] LC 432. All O\`one Data Structure
* [ ] LC 341. Flatten Nested List Iterator
* [ ] LC 642. Design Search Autocomplete System
* [ ] LC 170. Two Sum III - Data structure design
* [ ] LC 622. Design Circular Queue

{% tabs %}
{% tab title="427" %}
```python
class Node:
    def __init__(self, val, isLeaf, topLeft, topRight, bottomLeft, bottomRight):
        self.val = val
        self.isLeaf = isLeaf
        self.topLeft = topLeft
        self.topRight = topRight
        self.bottomLeft = bottomLeft
        self.bottomRight = bottomRight

class Solution:
    def construct(self, grid: List[List[int]]) -> 'Node':
        root = Node(True, True, None, None, None, None)
        if len(set([item for row in grid for item in row])) == 1:   #is leaf
            root.val = bool(grid[0][0])
        else:
            root.isLeaf = False
            size = len(grid)
            root.topLeft = self.construct([row[:size//2] for row in grid[:size//2]])
            root.topRight = self.construct([row[size//2:] for row in grid[:size//2]])
            root.bottomLeft = self.construct([row[:size//2] for row in grid[size//2:]])
            root.bottomRight = self.construct([row[size//2:] for row in grid[size//2:]])
        return root
```
{% endtab %}

{% tab title="1600" %}
```python
from collections import defaultdict
class ThroneInheritance:
    
    def __init__(self, kingName: str):
        self.dead = set()
        self.kingdom = defaultdict(list)
        self.root = kingName
        
    def birth(self, parentName: str, childName: str) -> None:
        self.kingdom[parentName].append(childName)
        
    def death(self, name: str) -> None:
        self.dead.add(name)
        
    def getInheritanceOrder(self) -> List[str]:
        res = []
        def dfs(root):
            if root not in self.dead:
                res.append(root)
            for nxt in self.kingdom[root]:
                dfs(nxt)
        dfs(self.root)
        return res
    
# TC:
# birth() -> O(1) || death -> O(1) || getInheritance -> O(N)
```
{% endtab %}

{% tab title="295" %}
```python
from heapq import *

'''
O(log n) add, O(1) find
'''
class MedianFinder:
    def __init__(self):
        self.small = []  # the smaller half of the list, max heap (invert min-heap)
        self.large = []  # the larger half of the list, min heap

    def addNum(self, num):
        if len(self.small) == len(self.large):
            # push to small
            heappush(self.small, -num)
            # rebalance
            heappush(self.large, -heappop(self.small))
            # heappush(self.large, -heappushpop(self.small, -num))
        else:
            # heappush(self.small, -heappushpop(self.large, num))
            # push to large
            heappush(self.large, num)
            # rebalance
            heappush(self.small, -heappop(self.large))

    def findMedian(self):
        
        if len(self.small) == len(self.large):
            return float(self.large[0] + (-self.small[0])) / 2.0
        else:
            return float(self.large[0])
        
```
{% endtab %}

{% tab title="480" %}
```python
# 1. using SortedList ======================================== O(n log k)
from sortedcontainers import SortedList

class Solution:
    def medianSlidingWindow(self, nums, k: int):
    	S = SortedList()
        # Initialize sorted list with first k - 1 elements of nums.
    	for i in range(k-1):
    		S.add(nums[i])  
            
    	i = k-1
    	res = []
    	while(i<len(nums)):
    		S.add(nums[i])
    		if k%2==0:
    			res.append((S[k//2 -1] + S[k//2])/2)
    		else:
    			res.append(S[k//2])
    		S.remove(nums[i-k+1])
    		i+=1
    	return res 
   

# 2. Using Two Heaps ============================================ O(NlogN) 
def medianSlidingWindow(self, nums, k):
        low = []  # lo is max-heap 
        high = [] # high is min-heap
        
        for i in range(k):
            heapq.heappush(high, (nums[i], i)) # high is a min-heap
            
        for _ in range(k>>1):
            self.convert(high, low)
            
        ans = [high[0][0]*1. if k&1 else (high[0][0]-low[0][0])/2]
        
        for i in range(len(nums[k:])):
            if nums[i+k] >= high[0][0]:
                heapq.heappush(high, (nums[i+k], i+k))
                if nums[i] <= high[0][0]: # keep the number of elements between two heap always in balance
                    self.convert(high, low)
            else:
                heapq.heappush(low, (-nums[i+k], i+k))
                if nums[i] >= high[0][0]:
                    self.convert(low, high)
            while low and low[0][1] <= i: heapq.heappop(low)
            while high and high[0][1] <= i: heapq.heappop(high)
            ans.append(high[0][0]*1. if k&1 else (high[0][0]-low[0][0])/2.)
        return ans
        
                    
    def convert(self, heap1, heap2): # convert min-heap1 to max-heap2
        element, index = heapq.heappop(heap1)
        heapq.heappush(heap2, (-element, index))
```
{% endtab %}
{% endtabs %}

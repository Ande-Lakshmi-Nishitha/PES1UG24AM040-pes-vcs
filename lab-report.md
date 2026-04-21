# PES Version Control System (PES-VCS)

## Student Details
Name: Ande Lakshmi Nishitha  
USN: PES1UG24AM040  

---

## Overview

This project implements a simplified version control system similar to Git. It supports content-addressable object storage using SHA-256, a tree-based directory structure, an index (staging area), and commit creation with history traversal.

---

## Phase 1: Object Storage

### test_objects Output

Command:
./test_objects

Output:
screenshot 1_a

### find .pes/objects -type f Output

Command:
find .pes/objects -type f

Output:
screenshot 1_b

---

## Phase 2: Tree Objects

### test_tree Output

Command:
./test_tree

Output:
screenshot 2_a

### Raw Tree Object

Command:
xxd .pes/objects/<tree-object> | head -20

Output:
screenshot 2_b

---

## Phase 3: Index

### pes status

Command:
./pes status

Output:
screenshot 3_a

---

### .pes/index Contents

Command:
cat .pes/index

Output:
screenshot 3_b

---

## Phase 4: Commits

### pes log

Command:
./pes log

Output:
screenshot 4_a

---

### Object Store Growth

Command:
find .pes -type f | sort

Output:
screenshot 4_b

---

### Reference Chain

Command:
cat .pes/refs/heads/main  
cat .pes/HEAD  

Output:
screenshot 4_c

---

## Analysis Questions

### Q5.1 Branch Checkout

A branch is stored as a file in .pes/refs/heads/ containing a commit hash. To implement pes checkout <branch>, we update .pes/HEAD to point to the selected branch and read the commit hash from the branch file. The corresponding commit and its tree are loaded, and the working directory is updated to match the tree by creating, modifying, or deleting files. The index must also be updated. This operation is complex due to handling overwrites, directory structures, and conflicts with uncommitted changes.

---

### Q5.2 Dirty Working Directory Detection

To detect a dirty working directory, the working directory is compared with the index using file metadata such as size and modification time. If differences are found, the file is considered modified. The index is then compared with the target branch’s tree. If a file is modified locally and also differs in the target branch, checkout must be refused to prevent data loss.

---

### Q5.3 Detached HEAD

In a detached HEAD state, HEAD points directly to a commit instead of a branch. New commits can still be created but are not referenced by any branch. These commits may become unreachable if the user switches branches. However, they can be recovered by creating a new branch pointing to the commit hash.

---

## Garbage Collection

### Q6.1 Unreachable Objects

Garbage collection identifies reachable objects starting from all branch heads. A traversal (DFS or BFS) is performed through commits, trees, and blobs. A hash set is used to track visited objects. Any object not visited is unreachable and can be deleted. In a repository with 100,000 commits and 50 branches, approximately 100,000 commits are visited due to shared history, along with associated trees and blobs.

---

### Q6.2 GC Race Condition

Running garbage collection concurrently with commit creation is dangerous because GC may delete objects that are not yet referenced. For example, a tree or blob may be written but not yet linked to a commit when GC runs. Git avoids this using atomic operations, temporary files, and delayed deletion, ensuring objects are not removed prematurely.

---

## Conclusion

This project successfully implements a simplified version control system with object storage, indexing, tree structures, and commit history. It demonstrates the core internal mechanisms of Git in a simplified form.

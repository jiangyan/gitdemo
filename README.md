# Git tutorial google slides here 👇:
https://docs.google.com/presentation/d/1imFmnP3nbAFk30GHz8ooFJBlR7aB-_F166-TGcpOoFk/edit?usp=sharing

<img width="1127" height="634" alt="image" src="https://github.com/user-attachments/assets/71e2ada9-0278-4f6c-8f37-bc400e5b060f" />

# Git Demo - Advanced Git Commands

This repository demonstrates advanced Git commands and version control concepts using custom aliases.

## Git Aliases

### `git bc` - Beyond Compare Integration
Compares two commits by extracting them to temporary directories and opening Beyond Compare 5 for visual diff.

```bash
[alias]
    bc = "!f() { \
        COMMIT1=${1:-   HEAD~1}; \
        COMMIT2=${2:-HEAD}; \
        HASH1=$(git rev-parse --short $COMMIT1); \
        HASH2=$(git rev-parse --short $COMMIT2); \
        CLEAN1=$(echo $COMMIT1 | sed 's/[~:\\/]/-/g'); \
        CLEAN2=$(echo $COMMIT2 | sed 's/[~:\\/]/-/g'); \
        TEMP1=\"D:/temp/git-compare-$CLEAN1-$HASH1\"; \
        TEMP2=\"D:/temp/git-compare-$CLEAN2-$HASH2\"; \
        echo \"Comparing $COMMIT1 ($HASH1) with $COMMIT2 ($HASH2)\"; \
        mkdir -p \"$TEMP1\" \"$TEMP2\"; \
        git archive $COMMIT1 | tar -x -C \"$TEMP1\"; \
        git archive $COMMIT2 | tar -x -C \"$TEMP2\"; \
        \"C:/Program Files/Beyond Compare 5/BCompare.exe\" \"$TEMP1\" \"$TEMP2\"; \
        rm -rf \"$TEMP1\" \"$TEMP2\"; \
        }; f"
```

**Usage:**
- `git bc` - compare previous commit with current
- `git bc main dev` - compare branches
- `git bc HEAD~3 HEAD` - compare specific commits

### `git tree` - Display Git Object Tree Structure
Shows the tree structure of a commit with object hashes, types, and modes.

```bash
tree = "!f() { \
    T=${1:-HEAD}; \
    git rev-parse $T >/dev/null 2>&1 || { echo \"Invalid: $T\"; exit 1; }; \
    SHA=$(git rev-parse $T); \
    TREE=$(git cat-file -p $SHA | grep '^tree' | awk '{print $2}'); \
    echo \"[commit] ${SHA:0:7}\"; \
    echo \"   -> [tree] ${TREE:0:7}\"; \
    echo; \
    git ls-tree -r -t $T | while IFS=$'\\t' read -r meta path; do \
        MODE=$(echo $meta | cut -d' ' -f1); \
        TYPE=$(echo $meta | cut -d' ' -f2); \
        HASH=$(echo $meta | cut -d' ' -f3); \
        DEPTH=$(($(echo \"$path\" | tr -cd '/' | wc -c))); \
        INDENT=$(printf '%*s' $((DEPTH * 2)) ''); \
        printf \"%-50s %s\\n\" \"$INDENT$MODE $TYPE ${HASH:0:7}\" \"$INDENT$path\"; \
    done; \
    echo; \
}; f"
```

**Usage:**
- `git tree` - show tree for HEAD
- `git tree main` - show tree for main branch
- `git tree abc123` - show tree for specific commit

### `git bctree` - Compare Tree Structures with Beyond Compare
Generates tree information for two commits and compares them using Beyond Compare.

```bash
bctree = "!f() { \
    COMMIT1=${1:-HEAD~1}; \
    COMMIT2=${2:-HEAD}; \
    TEMP1=\"D:/temp/tree-$(git rev-parse --short $COMMIT1).txt\"; \
    TEMP2=\"D:/temp/tree-$(git rev-parse --short $COMMIT2).txt\"; \
    mkdir -p D:/temp; \
    echo \"Generating tree info for $COMMIT1...\"; \
    { \
        SHA1=$(git rev-parse $COMMIT1); \
        TREE1=$(git cat-file -p $SHA1 | grep '^tree' | awk '{print $2}'); \
        echo \"[commit] ${SHA1:0:7} ($COMMIT1)\"; \
        echo \"   -> [tree] ${TREE1:0:7}\"; \
        echo; \
        git ls-tree -r -t $COMMIT1 | while IFS=$'\\t' read -r meta path; do \
            MODE=$(echo $meta | cut -d' ' -f1); \
            TYPE=$(echo $meta | cut -d' ' -f2); \
            HASH=$(echo $meta | cut -d' ' -f3); \
            DEPTH=$(($(echo \"$path\" | tr -cd '/' | wc -c))); \
            INDENT=$(printf '%*s' $((DEPTH * 2)) ''); \
            printf \"%-50s %s\\n\" \"$INDENT$MODE $TYPE ${HASH:0:7}\" \"$INDENT$path\"; \
        done; \
        echo; \
    } > \"$TEMP1\"; \
    echo \"Generating tree info for $COMMIT2...\"; \
    { \
        SHA2=$(git rev-parse $COMMIT2); \
        TREE2=$(git cat-file -p $SHA2 | grep '^tree' | awk '{print $2}'); \
        echo \"[commit] ${SHA2:0:7} ($COMMIT2)\"; \
        echo \"   -> [tree] ${TREE2:0:7}\"; \
        echo; \
        git ls-tree -r -t $COMMIT2 | while IFS=$'\\t' read -r meta path; do \
            MODE=$(echo $meta | cut -d' ' -f1); \
            TYPE=$(echo $meta | cut -d' ' -f2); \
            HASH=$(echo $meta | cut -d' ' -f3); \
            DEPTH=$(($(echo \"$path\" | tr -cd '/' | wc -c))); \
            INDENT=$(printf '%*s' $((DEPTH * 2)) ''); \
            printf \"%-50s %s\\n\" \"$INDENT$MODE $TYPE ${HASH:0:7}\" \"$INDENT$path\"; \
        done; \
        echo; \
    } > \"$TEMP2\"; \
    echo \"Opening Beyond Compare...\"; \
    bcompare \"$TEMP1\" \"$TEMP2\"; \
    rm -f \"$TEMP1\" \"$TEMP2\"; \
}; f"
```

**Usage:**
- `git bctree` - compare tree structure between HEAD~1 and HEAD
- `git bctree main dev` - compare tree structures between branches
- `git bctree v1.0 v2.0` - compare tree structures between tags

## Installation

To use these aliases, add them to your `.gitconfig` file or run:

```bash
git config alias.bc "!f() { ... }; f"
git config alias.tree "!f() { ... }; f"
git config alias.bctree "!f() { ... }; f"
```

## Requirements

- Git (any recent version)
- Beyond Compare 5 installed at `C:/Program Files/Beyond Compare 5/BCompare.exe`
- Windows environment with bash/shell support
- `tar` command available (included with Git for Windows)

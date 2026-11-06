# drydiff

**drydiff** is a small dry-run comparison tool for local and remote paths.

It wraps `rsync` and presents a compact, readable summary of differences without transferring or modifying any files. The goal is simple: show what is the same, what differs, and what exists only on one side.

## Features

- **Dry-run only** - no files are transferred or changed
- **Checksum comparison** - uses `rsync --checksum (-c)` for accurate content checks
- **Local and remote paths** - supports SSH options via `-e` / `--rsh`
- **Clear status labels** - `+SRC`, `+DST`, `DIFF`, `META`
- **Compact output** - one line per item, with type and path
- **Color output** - enabled on TTY, disabled with `NO_COLOR` or when piped
- **No dependencies beyond basics** - requires Bash, `rsync`, and `awk`

## Requirements

| Tool | Notes |
|------|-------|
| Bash | 4+ recommended |
| rsync | Required |
| awk | Required |
| ssh | Only for remote comparisons |

## Installation

```bash
# Make executable
chmod +x drydiff.sh

# Symlink into PATH (recommended)
ln -s /path/to/drydiff.sh ~/.local/bin/drydiff

# Or add an alias
echo "alias drydiff='/path/to/drydiff.sh'" >> ~/.bashrc
```

## Usage

### Basic comparison

```bash
drydiff ./local/ ./backup/
drydiff /path/to/source/ user@host:/path/to/destination/
```

### Custom SSH options

```bash
drydiff -e "ssh -i ~/.ssh/id_ed25519 -p 2222" ./local/ user@host:/remote/
drydiff --rsh="ssh -J jump@bastion" ./src/ user@host:/dst/
```

### Help and version

```bash
drydiff --help
drydiff --version
```

## Output

Example output:

```text
DryDiff v1.0.0
SRC: ./local/
DST: user@host:/remote/
RSH: ssh -i ~/.ssh/id_ed25519 -p 2222

T  STAT   PATH
----------------------------------------
f  +SRC   config/app.yaml
f  +DST   old.log
d  +SRC   assets/
f  DIFF   README.md
f  META   scripts/deploy.sh
```

### Legend

| Field | Meaning |
|-------|---------|
| `T` | Item type from rsync (`f` file, `d` directory, `L` symlink, `D` device, `S` socket, etc.) |
| `STAT` | Comparison status |
| `PATH` | Relative path |

| Status | Meaning |
|--------|---------|
| `+SRC` | Present only on source |
| `+DST` | Present only on destination |
| `DIFF` | Content differs |
| `META` | Metadata differs (permissions, timestamps, owner, group, ACL/xattrs, etc.) |

## Notes

- **Dry-run only**: `drydiff` never transfers or modifies files.
- **Trailing slash matters**: `dir/` compares the contents of the directory; `dir` compares the directory itself. This follows `rsync` behavior.
- **Checksum mode**: comparison uses `--checksum (-c)` for accuracy. It can be slower on large trees.
- **Destination-only items**: the tool uses `--delete` in dry-run mode so items present only on the destination are reported as `+DST`.
- **Exit code**: the command returns `rsync`'s exit status (useful in scripts).

## License

MIT. See [LICENSE](LICENSE).

@AGENTS.md

# Fork notes (pdeveltere/herdr-app)

This is a personal fork of `herdrdev/herdr-app`. Its feature work lives on the
`space-folders` branch: folders for spaces in the sidebar.

## The feature

- `defaults/mods/spaces/main.js`: the sidebar. Folders, nested folders, drag and
  drop (spaces and folder headers), the drag card, top-level drop zone, status
  dots on space rows and folder headers. The context menu it opens is
  `props().menu ?? "menu"`.
- `defaults/mods/menu/main.js`: "Move to folder..." for spaces (`tab: "space"`)
  and folder headers (`tab: "folder"`).
- `defaults/mods/space-folder/`: the move-to-folder dialog
  (props `{ space }` or `{ folder }`).
- `src/mods/render_tests.rs`: scenarios for the dialog and the folder menu.
- Folder data: `shared("space.folders")` =
  `[{ name, collapsed, ids: [spaceId], parent: folderName | null }]`, stored in
  `~/.config/herdr-app/shared.json`. A folder stays while it holds a space or a
  folder that stays.

## How the installed app runs this code

The person runs the released Herdr.app, not a build of this fork. Herdr replaces
its shipped mods on every start, so the live app uses personal copies under other
names, which are **symlinks into this repo**:

| `~/.config/herdr-app/mods/` | points to |
|---|---|
| `spaces-folders` | `defaults/mods/spaces` |
| `menu-folders` | `defaults/mods/menu` |
| `space-folder` | `defaults/mods/space-folder` |

The layout `~/.config/herdr-app/layouts/main-folders.json` (a clone of `main`,
not in this repo) uses `spaces-folders` with props `{ "menu": "menu-folders" }`.
Back to stock: `herdr-app do layout.switch '{"name":"main"}'`.

**Edit only in this repo.** The app's file watcher doesn't follow symlinks, so
after an edit reload the mods by touching the links:

```sh
touch -h ~/.config/herdr-app/mods/{spaces-folders,menu-folders,space-folder}
```

Then verify: `herdr-app check ~/.config/herdr-app/mods/<mod>` for each changed
mod, `herdr-app status` (no `errors`), and `herdr-app shot <file.png>` to look.

## Building and testing

`vendor/ghostty` is a submodule (`git submodule update --init --recursive`).
The build needs Zig 0.16 exactly (installed at
`~/.local/share/zig/zig-aarch64-macos-0.16.0/zig`) and Rust 1.95.0 (CI's
`RUST_TOOLCHAIN`; the default toolchain is too old):

```sh
ZIG=~/.local/share/zig/zig-aarch64-macos-0.16.0/zig cargo +1.95.0 test --locked
ZIG=~/.local/share/zig/zig-aarch64-macos-0.16.0/zig cargo +1.95.0 build --release
```

`just` isn't installed; use cargo directly.

## Unrelated

`~/Workspace/oss/herdr` (fork `pdeveltere/herdr`, branch `space-folders`) is the
terminal-only herdr with an earlier, client-only take on folders. The person
doesn't use that program; don't confuse the two.

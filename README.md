# ryoku-status-tray-toggle

A fork of Ryoku's built-in **Status** bar widget (QS Bar, gid `G3`: Arch
update · tray · notifications) that adds a settings toggle to hide the
system-tray icons cluster while leaving the notification bell visible.

This is **not** a Ryoku plugin — the Status widget's tray and notification
internals (`shell.services.Tray`, the shell's notification state) aren't
reachable from the plugin sandbox, so this uses Ryoku's supported file-fork
mechanism instead: the user overlay at `~/.config/ryoku/user_edits/`, which
mirrors `~/.config` and wins over Ryoku's shipped copies on every update.

## What changed

- `quickshell/shell/modules/bar/barstyles/qsbar/core/widgets.json` — adds a
  `trayHidden` (`toggle`) setting to the `status` widget's catalog entry, so
  it renders as a native control in QS Bar Settings and is accepted by
  `ryoku-shell bar set`.
- `quickshell/shell/modules/bar/barstyles/qsbar/Theme.qml` — adds a
  `trayHidden` property, persisted to `shell.json` the same way the existing
  `audioBoost` setting is (load in `applyStudioSettings`, save in
  `persistWidgetsToConfig`).
- `quickshell/shell/modules/bar/barstyles/qsbar/modules/TrayWidget.qml` —
  collapses the tray widget to zero width/invisible when `trayHidden` is
  true; the notification bell (`NotificationWidget.qml`) is untouched.

## Install

Copy the `quickshell/` tree here into Ryoku's user overlay, which mirrors
`~/.config` and survives `ryoku update`:

```bash
cp -r quickshell ~/.config/ryoku/user_edits/
ryoku materialize
ryoku reload
```

## Use

```bash
# toggle from a terminal
ryoku-shell bar set status trayHidden true   # hide tray icons
ryoku-shell bar set status trayHidden false  # show tray icons again

# or from the GUI
ryoku-shell bar settings widgets   # QS Bar Settings > Widgets > Status
```

## Undo

```bash
ryoku reset quickshell/shell/modules/bar/barstyles/qsbar/core/widgets.json
ryoku reset quickshell/shell/modules/bar/barstyles/qsbar/Theme.qml
ryoku reset quickshell/shell/modules/bar/barstyles/qsbar/modules/TrayWidget.qml
```

This drops each file back to Ryoku's shipped version.

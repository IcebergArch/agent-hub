# VS Code IDEA-like 本机配置快照

日期：2026-06-17
类型：runbooks
项目：personal-agent
来源：Codex 按用户要求保存当前 VS Code / Markdown / Git / Terminal 调整
版式：配置快照，恢复路径优先

## Summary

这份快照保存当前本机 VS Code 接近 IntelliJ IDEA / GoLand 体验的用户级配置，主要包括主题、编辑器交互、Markdown 预览、Mermaid 架构图样式、Git/GitLens/Git Graph、终端布局、快捷键和自定义 Markdown CSS。

这些内容来自用户级配置，不是项目级配置，不应写入公司或个人项目仓库。开启 VS Code Settings Sync 时，它们可能同步到 VS Code 云端服务；不开启同步时仅保留在本机。

## Scope

- VS Code 用户 settings：`/Users/shatang/Library/Application Support/Code/User/settings.json`
- VS Code 用户 keybindings：`/Users/shatang/Library/Application Support/Code/User/keybindings.json`
- VS Code Markdown 预览 CSS：`/Users/shatang/Library/Application Support/Code/User/idea-markdown-preview.css`
- Markdown Preview Enhanced 样式：`/Users/shatang/.local/state/crossnote/style.less`
- VS Code 插件目录清单：`/Users/shatang/.vscode/extensions`
- 终端 prompt：只保存 `.zshrc` 中 Codex 标记的 compact IDEA prompt block，不保存完整 `.zshrc`，避免带入环境变量或凭据。

## Security Notes

- 本次未复制完整 `.zshrc`。
- settings key 敏感词扫描结果：未发现 token / secret / password / credential / auth 等命名的配置项。
- 本快照仍包含本机绝对路径，恢复到其他机器时需要按路径调整。

## Restore Checklist

1. 将 `settings.json` 内容恢复到 VS Code User settings。
2. 将 `keybindings.json` 内容恢复到 VS Code User keybindings。
3. 将两个 Markdown CSS 文件恢复到原路径，或同步修改 `markdown.styles` / MPE style 路径。
4. 按插件清单安装必要插件，重点是 IDEA theme/keymap、Markdown Preview Enhanced、GitLens、Git Graph。
5. 如果需要终端 prompt，只复制本报告里的 compact prompt block 到 `.zshrc` 末尾。
6. 重启 VS Code 或运行 Developer: Reload Window。

## Installed Extensions Snapshot

- `alefragnani.project-manager-13.1.0`
- `bierner.markdown-preview-github-styles-2.2.0`
- `christian-kohler.npm-intellisense-1.4.5`
- `compassak.intellij-idea-new-ui-1.4.0`
- `davidanson.vscode-markdownlint-0.61.2`
- `eamodio.gitlens-18.2.0`
- `golang.go-0.54.0`
- `k--kato.intellij-idea-keybindings-1.7.7`
- `mhutchie.git-graph-1.30.0`
- `ms-python.debugpy-2026.6.0-darwin-arm64`
- `ms-python.python-2026.4.0-darwin-arm64`
- `ms-python.vscode-pylance-2026.2.1`
- `ms-python.vscode-python-envs-1.34.0-darwin-arm64`
- `ms-vscode.vscode-chat-customizations-evaluations-1.0.7`
- `mtxr.sqltools-0.28.5`
- `shd101wyy.markdown-preview-enhanced-0.8.30`
- `takumii.markdowntable-0.13.0`
- `timgreen.vscode-auto-open-markdown-preview-enhanced-0.0.5`
- `yierbubu.md-auto-preview-1.0.29`
- `yzhang.markdown-all-in-one-3.6.3`

## keybindings.json

```json
[
    {
        "key": "shift+enter",
        "command": "workbench.action.terminal.sendSequence",
        "args": {
            "text": "\u001b\r"
        },
        "when": "terminalFocus"
    },
    {
        "key": "cmd+j",
        "command": "workbench.action.togglePanel"
    }
]
```

## settings.json

```json
{
    "terminal.integrated.mouseWheelScrollSensitivity": 3,
    "terminal.integrated.gpuAcceleration": "off",
    "workbench.colorTheme": "IntelliJ IDEA New UI Dark",
    "workbench.colorCustomizations": {
        "peekView.border": "#E0AF68",
        "peekViewTitle.background": "#3B3F46",
        "peekViewTitleLabel.foreground": "#FFFFFF",
        "peekViewTitleDescription.foreground": "#C7C7C7",
        "peekViewEditor.background": "#1B1D21",
        "peekViewEditorGutter.background": "#1B1D21",
        "peekViewEditor.matchHighlightBackground": "#FFCC0055",
        "peekViewEditor.matchHighlightBorder": "#FFCC00",
        "peekViewResult.background": "#252830",
        "peekViewResult.fileForeground": "#FFFFFF",
        "peekViewResult.lineForeground": "#C8C8C8",
        "peekViewResult.matchHighlightBackground": "#FFCC0044",
        "peekViewResult.selectionBackground": "#3F5F9F",
        "peekViewResult.selectionForeground": "#FFFFFF",
        "widget.border": "#5A606B",
        "widget.shadow": "#000000CC",
        "editorHoverWidget.background": "#2B2D33",
        "editorHoverWidget.foreground": "#E6E6E6",
        "editorHoverWidget.border": "#5A606B",
        "editorHoverWidget.highlightForeground": "#E0AF68",
        "editorHoverWidget.statusBarBackground": "#24272D",
        "editorWidget.background": "#2B2D33",
        "editorWidget.foreground": "#E6E6E6",
        "editorWidget.border": "#5A606B",
        "editorWidget.resizeBorder": "#E0AF68",
        "editorSuggestWidget.background": "#2B2D33",
        "editorSuggestWidget.foreground": "#D8DEE9",
        "editorSuggestWidget.border": "#5A606B",
        "editorSuggestWidget.selectedBackground": "#3F5F9F",
        "editorSuggestWidget.selectedForeground": "#FFFFFF",
        "editorSuggestWidget.highlightForeground": "#E0AF68",
        "editorSuggestWidget.focusHighlightForeground": "#FFCC66",
        "notifications.background": "#2B2D33",
        "notifications.foreground": "#E6E6E6",
        "notifications.border": "#5A606B",
        "notificationCenter.border": "#5A606B",
        "notificationToast.border": "#5A606B",
        "focusBorder": "#E0AF68",
        "gitDecoration.addedResourceForeground": "#629755",
        "gitDecoration.untrackedResourceForeground": "#629755",
        "gitDecoration.modifiedResourceForeground": "#6897BB",
        "gitDecoration.deletedResourceForeground": "#CC6666",
        "gitDecoration.renamedResourceForeground": "#B8A05A",
        "gitDecoration.ignoredResourceForeground": "#6B7078",
        "gitDecoration.conflictingResourceForeground": "#C17D11",
        "gitDecoration.submoduleResourceForeground": "#7A8FA8",
        "gitDecoration.stageModifiedResourceForeground": "#6897BB",
        "gitDecoration.stageDeletedResourceForeground": "#CC6666",
        "editorGutter.addedBackground": "#4D7F45",
        "editorGutter.modifiedBackground": "#5F83A5",
        "editorGutter.deletedBackground": "#B95E5E",
        "minimapGutter.addedBackground": "#4D7F45",
        "minimapGutter.modifiedBackground": "#5F83A5",
        "minimapGutter.deletedBackground": "#B95E5E",
        "diffEditor.insertedTextBackground": "#2F5738AA",
        "diffEditor.removedTextBackground": "#633333AA",
        "diffEditor.insertedLineBackground": "#243F2BAA",
        "diffEditor.removedLineBackground": "#442B2BAA",
        "diffEditor.diagonalFill": "#3C3F44",
        "tree.indentGuidesStroke": "#44484F",
        "tree.inactiveIndentGuidesStroke": "#31343A",
        "diffEditor.border": "#3C3F44",
        "sideBar.background": "#1B1D21",
        "sideBar.foreground": "#C9CCD3",
        "sideBar.border": "#2A2D33",
        "sideBarSectionHeader.background": "#202329",
        "sideBarSectionHeader.foreground": "#D7DAE0",
        "sideBarSectionHeader.border": "#2F333A",
        "badge.background": "#3A3D45",
        "badge.foreground": "#D7DAE0",
        "activityBarBadge.background": "#4E6B55",
        "activityBarBadge.foreground": "#FFFFFF",
        "panel.background": "#1B1D21",
        "panel.border": "#2D3138",
        "tab.activeBackground": "#20242B",
        "tab.activeForeground": "#E1E4EA",
        "tab.activeBorder": "#526A88",
        "tab.activeBorderTop": "#526A88",
        "tab.hoverBackground": "#252A32",
        "tab.border": "#2A2D33"
    },
    "references.preferredLocation": "view",
    "editor.gotoLocation.multipleReferences": "peek",
    "editor.gotoLocation.multipleDefinitions": "peek",
    "js/ts.referencesCodeLens.enabled": true,
    "editor.hover.enabled": "on",
    "editor.hover.sticky": true,
    "editor.hover.delay": 250,
    "editor.hover.hidingDelay": 600000,
    "workbench.editor.enablePreview": false,
    "workbench.editor.enablePreviewFromQuickOpen": false,
    "workbench.list.openMode": "singleClick",
    "explorer.autoReveal": true,
    "explorer.compactFolders": false,
    "workbench.tree.indent": 18,
    "breadcrumbs.enabled": true,
    "editor.stickyScroll.enabled": true,
    "editor.stickyScroll.maxLineCount": 5,
    "editor.peekWidgetDefaultFocus": "tree",
    "editor.definitionLinkOpensInPeek": true,
    "editor.parameterHints.enabled": true,
    "editor.parameterHints.cycle": true,
    "editor.linkedEditing": true,
    "editor.lightbulb.enabled": "on",
    "go.useLanguageServer": true,
    "go.testExplorer.packageDisplayMode": "nested",
    "editor.inlayHints.enabled": "on",
    "go.inlayHints.parameterNames": true,
    "go.inlayHints.compositeLiteralFields": true,
    "markdown.styles": [
        "/Users/shatang/Library/Application Support/Code/User/idea-markdown-preview.css"
    ],
    "markdown.preview.fontSize": 14,
    "markdown.preview.lineHeight": 1.45,
    "markdown.preview.markEditorSelection": true,
    "markdown.preview.scrollPreviewWithEditor": true,
    "markdown.preview.scrollEditorWithPreview": true,
    "markdown.preview.openMarkdownLinks": "inPreview",
    "markdown-preview-enhanced.previewTheme": "none.css",
    "markdown-preview-enhanced.codeBlockTheme": "darcula.css",
    "markdown-preview-enhanced.enableScriptExecution": true,
    "editor.fontFamily": "'JetBrains Mono', 'SF Mono', Menlo, Monaco, 'Courier New', monospace",
    "editor.lineHeight": 21,
    "editor.fontLigatures": false,
    "editor.letterSpacing": 0,
    "editor.padding.top": 4,
    "editor.padding.bottom": 4,
    "terminal.integrated.fontFamily": "'JetBrains Mono', 'SF Mono', Menlo, Monaco, 'Courier New', monospace",
    "terminal.integrated.fontSize": 13,
    "terminal.integrated.lineHeight": 1.15,
    "debug.console.fontFamily": "'JetBrains Mono', 'SF Mono', Menlo, Monaco, 'Courier New', monospace",
    "debug.console.fontSize": 14,
    "markdown.preview.fontFamily": "-apple-system, BlinkMacSystemFont, 'Inter', 'SF Pro Text', 'Segoe UI', 'PingFang SC', 'Microsoft YaHei', sans-serif",
    "markdown-preview-enhanced.automaticallyShowPreviewOfMarkdownBeingEdited": true,
    "markdown-preview-enhanced.previewMode": "Previews Only",
    "workbench.editorAssociations": {
        "*.copilotmd": "vscode.markdown.preview.editor",
        "*.md": "markdown-preview-enhanced",
        "*.markdown": "markdown-preview-enhanced",
        "*.mdown": "markdown-preview-enhanced",
        "*.mkd": "markdown-preview-enhanced",
        "*.mkdn": "markdown-preview-enhanced",
        "*.rmd": "markdown-preview-enhanced",
        "*.qmd": "markdown-preview-enhanced",
        "*.mdx": "markdown-preview-enhanced"
    },
    "editor.formatOnSave": true,
    "explorer.confirmDelete": false,
    "git.decorations.enabled": true,
    "explorer.decorations.enabled": true,
    "explorer.decorations.colors": true,
    "explorer.decorations.badges": true,
    "problems.decorations.enabled": true,
    "scm.diffDecorations": "all",
    "scm.diffDecorationsGutterWidth": 3,
    "diffEditor.renderSideBySide": true,
    "diffEditor.ignoreTrimWhitespace": false,
    "diffEditor.renderIndicators": true,
    "workbench.tree.renderIndentGuides": "always",
    "scm.alwaysShowRepositories": false,
    "gitlens.ai.model": "vscode",
    "gitlens.ai.vscode.model": "copilotcli:claude-haiku-4.5",
    "scm.defaultViewMode": "tree",
    "git-graph.graph.style": "rounded",
    "git-graph.graph.colours": [
        "#6F849C",
        "#8B7A60",
        "#72836A",
        "#7C748B",
        "#876F72",
        "#6D7F74",
        "#8A6A45",
        "#777D87"
    ],
    "git-graph.referenceLabels.combineLocalAndRemoteBranchLabels": true,
    "git-graph.referenceLabels.alignment": "Branches (aligned to the graph) & Tags (on the right)",
    "git-graph.commitDetailsView.location": "Docked to Bottom",
    "git-graph.commitDetailsView.fileView.fileTree.compactFolders": false,
    "git-graph.defaultColumnVisibility": {
        "Date": true,
        "Author": true,
        "Commit": true
    },
    "git-graph.repository.onLoad.scrollToHead": true,
    "git-graph.repository.onLoad.showCheckedOutBranch": false,
    "git-graph.repository.showRemoteBranches": true,
    "git-graph.repository.showTags": true,
    "git-graph.repository.showStashes": true,
    "git-graph.repository.showUncommittedChanges": true,
    "git-graph.repository.showUntrackedFiles": false,
    "git-graph.repository.commits.mute.mergeCommits": true,
    "git-graph.repository.commits.mute.commitsThatAreNotAncestorsOfHead": true,
    "git-graph.repository.fetchAndPrune": true,
    "git-graph.sourceCodeProviderIntegrationLocation": "Inline",
    "git-graph.showStatusBarItem": true,
    "gitlens.currentLine.enabled": true,
    "gitlens.codeLens.enabled": false,
    "gitlens.hovers.currentLine.over": "line",
    "gitlens.views.branches.files.layout": "tree",
    "gitlens.views.commits.files.layout": "tree",
    "gitlens.views.remotes.files.layout": "tree",
    "gitlens.views.stashes.files.layout": "tree",
    "terminal.integrated.cursorStyle": "line",
    "terminal.integrated.cursorWidth": 1,
    "terminal.integrated.minimumContrastRatio": 4.5,
    "workbench.sideBar.location": "left",
    "workbench.panel.defaultLocation": "bottom",
    "workbench.panel.opensMaximized": "never",
    "workbench.panel.showLabels": false,
    "workbench.layoutControl.enabled": false,
    "workbench.statusBar.visible": true,
    "workbench.editor.showTabs": "multiple",
    "workbench.editor.tabSizing": "fixed",
    "workbench.editor.tabSizingFixedMinWidth": 90,
    "workbench.editor.tabSizingFixedMaxWidth": 180,
    "workbench.editor.pinnedTabSizing": "compact",
    "workbench.editor.pinnedTabsOnSeparateRow": false,
    "workbench.editor.labelFormat": "short",
    "workbench.editor.openSideBySideDirection": "right",
    "workbench.editor.splitSizing": "auto",
    "workbench.editor.editorActionsLocation": "default",
    "terminal.integrated.defaultLocation": "view",
    "terminal.integrated.tabs.enabled": true,
    "terminal.integrated.tabs.hideCondition": "never",
    "terminal.integrated.tabs.showActions": "singleTerminalOrNarrow",
    "terminal.integrated.tabs.showActiveTerminal": "always",
    "terminal.integrated.hideOnStartup": "never",
    "terminal.integrated.focusAfterRun": "terminal",
    "terminal.integrated.shellIntegration.decorationsEnabled": "never",
    "terminal.integrated.stickyScroll.enabled": false,
    "terminal.integrated.tabs.location": "left",
    "terminal.integrated.tabs.focusMode": "singleClick",
    "gitlens.currentLine.scrollable": false,
    "gitlens.currentLine.format": "${author}, ${agoOrDate} • ${message|60?}",
    "gitlens.currentLine.uncommittedChangesFormat": "Uncommitted changes",
    "gitlens.hovers.enabled": true,
    "gitlens.hovers.currentLine.enabled": true,
    "gitlens.hovers.currentLine.details": true,
    "gitlens.hovers.currentLine.changes": true,
    "gitlens.blame.compact": true,
    "gitlens.blame.format": "${author}, ${agoOrDate} • ${message|80?}",
    "gitlens.blame.highlight.enabled": true,
    "gitlens.blame.highlight.locations": [
        "gutter",
        "line",
        "overview"
    ],
    "gitlens.statusBar.enabled": true,
    "gitlens.statusBar.command": "gitlens.toggleFileBlame",
    "gitlens.statusBar.format": "${author}, ${agoOrDate}",
    "terminal.integrated.tabs.showDetailed": false,
    "gitlens.graph.defaultItemLimit": 200,
    "gitlens.graph.pageItemLimit": 100,
    "gitlens.graph.layout": "editor",
    "gitlens.graph.sidebar.enabled": false,
    "gitlens.graph.sidebar.pinned": false,
    "gitlens.graph.details.location": "bottom",
    "gitlens.graph.minimap.enabled": false,
    "gitlens.graph.scrollMarkers.enabled": false,
    "gitlens.graph.experimental.visualizations.enabled": false,
    "gitlens.graph.experimental.kanban.enabled": false,
    "gitlens.graph.avatars": false,
    "gitlens.graph.issues.enabled": false,
    "gitlens.graph.pullRequests.enabled": false,
    "gitlens.graph.showGhostRefsOnRowHover": false,
    "gitlens.graph.highlightRowsOnRefHover": true,
    "gitlens.graph.branchesVisibility": "smart",
    "gitlens.graph.showRemoteNames": false,
    "gitlens.graph.showUpstreamStatus": false,
    "gitlens.graph.showWorktreeWipStats": false,
    "gitlens.graph.dimMergeCommits": true,
    "gitlens.graph.dateStyle": "relative",
    "gitlens.graph.commitOrdering": "date",
    "gitlens.graph.searchAutocompleteOnFocus": false,
    "gitlens.views.commitDetails.avatars": false,
    "gitlens.views.commitDetails.pullRequests.enabled": false,
    "gitlens.views.commitDetails.files.layout": "tree",
    "gitlens.views.commitDetails.files.compact": false,
    "gitlens.views.branches.avatars": false,
    "gitlens.views.branches.pullRequests.enabled": false,
    "gitlens.views.commits.avatars": false,
    "gitlens.views.commits.pullRequests.enabled": false,
    "gitlens.views.remotes.avatars": false,
    "gitlens.views.remotes.pullRequests.enabled": false,
    "gitlens.views.repositories.avatars": false,
    "gitlens.views.repositories.pullRequests.enabled": false,
    "gitlens.views.showRelativeDateMarkers": false,
    "markdown-preview-enhanced.mermaidTheme": "dark"
}
```

## idea-markdown-preview.css

```css
/* JetBrains Darcula/New UI style for VS Code Markdown previews. */
:root {
  --jb-bg: #1e1f22;
  --jb-bg-soft: #202124;
  --jb-panel: #2b2d30;
  --jb-panel-soft: #26282d;
  --jb-border: #3c3f44;
  --jb-border-soft: #34373d;
  --jb-text: #bcbec4;
  --jb-text-strong: #dfe1e5;
  --jb-text-muted: #8d929a;
  --jb-blue: #548af7;
  --jb-blue-soft: rgba(84, 138, 247, 0.16);
  --jb-yellow: #d6b46d;
  --jb-orange: #cc7832;
  --jb-green: #6aab73;
  --jb-purple: #bc77b9;
  --jb-red: #cf6679;
}

html,
body {
  background: var(--jb-bg) !important;
}

body.vscode-body,
.vscode-body {
  box-sizing: border-box;
  max-width: 1120px;
  margin: 0 auto !important;
  padding: 24px 40px 48px !important;
  color: var(--jb-text) !important;
  background: var(--jb-bg) !important;
  font-family: -apple-system, BlinkMacSystemFont, 'Inter', 'SF Pro Text', 'Segoe UI', 'PingFang SC', 'Microsoft YaHei', sans-serif;
  font-size: 14px;
  line-height: 1.45;
  letter-spacing: 0;
}

body.vscode-body::selection,
.vscode-body *::selection {
  color: #ffffff;
  background: rgba(84, 138, 247, 0.42);
}

h1,
h2,
h3,
h4,
h5,
h6 {
  color: var(--jb-text-strong) !important;
  font-weight: 680;
  line-height: 1.22;
  letter-spacing: 0;
}

h1 {
  margin: 0 0 20px;
  padding-bottom: 12px;
  border-bottom: 1px solid var(--jb-border-soft);
  font-size: 28px;
}

h2 {
  margin-top: 28px;
  margin-bottom: 14px;
  padding-bottom: 7px;
  border-bottom: 1px solid #30333a;
  font-size: 21px;
}

h3 {
  margin-top: 21px;
  margin-bottom: 10px;
  font-size: 17px;
}

h4 {
  margin-top: 17px;
  margin-bottom: 8px;
  font-size: 15px;
}

p,
li {
  color: var(--jb-text) !important;
}

p {
  margin: 8px 0 12px;
}

ul,
ol {
  margin-top: 6px;
  margin-bottom: 12px;
}

strong,
b {
  color: var(--jb-text-strong) !important;
  font-weight: 680;
}

em {
  color: #c9cbd1;
}

a {
  color: var(--jb-blue) !important;
  text-decoration: none;
}

a:hover {
  color: #6ca0ff !important;
  text-decoration: underline;
}

hr {
  border: 0;
  border-top: 1px solid var(--jb-border-soft);
}

blockquote {
  margin: 12px 0;
  padding: 8px 12px;
  color: #aeb4bd !important;
  border-left: 3px solid #5f7fbf;
  background: #24262b;
}

:not(pre) > code {
  padding: 1px 5px;
  border: 1px solid #3b3e45;
  border-radius: 4px;
  color: #d7dce2 !important;
  background: #2d3036 !important;
  font-family: 'JetBrains Mono', 'SF Mono', Menlo, Monaco, 'Courier New', monospace;
  font-size: 0.9em;
}

pre {
  margin: 12px 0 16px;
  padding: 10px 13px !important;
  overflow: auto;
  border: 1px solid var(--jb-border-soft);
  border-radius: 5px;
  background: var(--jb-panel) !important;
  box-shadow: none !important;
}

pre code,
code[class*='language-'],
pre[class*='language-'] {
  color: #c9ccd3 !important;
  background: transparent !important;
  font-family: 'JetBrains Mono', 'SF Mono', Menlo, Monaco, 'Courier New', monospace;
  font-size: 13px;
  line-height: 1.38;
  letter-spacing: 0;
  white-space: pre;
  text-shadow: none !important;
}

.hljs-keyword,
.hljs-selector-tag,
.hljs-literal,
.hljs-name,
.token.keyword,
.token.selector,
.token.important,
.token.atrule {
  color: var(--jb-orange) !important;
}

.hljs-string,
.hljs-regexp,
.hljs-symbol,
.hljs-bullet,
.token.string,
.token.char,
.token.regex,
.token.attr-value {
  color: var(--jb-green) !important;
}

.hljs-number,
.hljs-attr,
.hljs-variable,
.hljs-template-variable,
.token.number,
.token.boolean,
.token.constant,
.token.property {
  color: var(--jb-yellow) !important;
}

.hljs-comment,
.hljs-quote,
.token.comment,
.token.prolog,
.token.doctype,
.token.cdata {
  color: #7a8089 !important;
  font-style: italic;
}

.token.function,
.hljs-title,
.hljs-function {
  color: #ffc66d !important;
}

.token.operator,
.token.punctuation {
  color: #bcbec4 !important;
}

.code-active-line {
  background: rgba(84, 138, 247, 0.10) !important;
}

.code-line:hover {
  background: rgba(255, 255, 255, 0.03) !important;
}

table {
  display: block;
  width: 100%;
  overflow: auto;
  border-collapse: collapse;
  font-size: 13px;
  color: var(--jb-text) !important;
}

th,
td {
  padding: 6px 9px;
  border: 1px solid var(--jb-border) !important;
}

th {
  color: var(--jb-text-strong) !important;
  background: #25272c !important;
  font-weight: 680;
}

td {
  background: #1f2024 !important;
}

tr:nth-child(2n) td {
  background: #222429 !important;
}

img {
  max-width: 100%;
  border-radius: 5px;
}

mark {
  color: #111318;
  background: #d6b46d;
}

@media (max-width: 900px) {
  body.vscode-body,
  .vscode-body {
    padding: 20px 24px 42px !important;
  }

  h1 {
    font-size: 24px;
  }
}

/* >>> codex-mermaid-idea-dark >>> */
.markdown-preview.markdown-preview .mermaid,
.markdown-preview.markdown-preview pre.mermaid,
.markdown-preview.markdown-preview div.mermaid {
  box-sizing: border-box;
  margin: 14px 0 20px;
  padding: 12px 14px;
  overflow: auto;
  border: 1px solid #34373d !important;
  border-radius: 6px;
  background: #1e1f22 !important;
}

.markdown-preview.markdown-preview .mermaid svg {
  display: block;
  max-width: 100%;
  height: auto;
  margin: 0 auto;
  background: transparent !important;
}

.markdown-preview.markdown-preview .mermaid .node rect,
.markdown-preview.markdown-preview .mermaid .node polygon,
.markdown-preview.markdown-preview .mermaid .node circle,
.markdown-preview.markdown-preview .mermaid .node ellipse {
  fill: #25272c !important;
  stroke: #6f849c !important;
  stroke-width: 1.25px !important;
}

.markdown-preview.markdown-preview .mermaid .cluster rect {
  fill: #202124 !important;
  stroke: #3c3f44 !important;
  stroke-width: 1px !important;
}

.markdown-preview.markdown-preview .mermaid .label,
.markdown-preview.markdown-preview .mermaid .nodeLabel,
.markdown-preview.markdown-preview .mermaid .cluster-label,
.markdown-preview.markdown-preview .mermaid .edgeLabel,
.markdown-preview.markdown-preview .mermaid text,
.markdown-preview.markdown-preview .mermaid span {
  color: #c7cbd3 !important;
  fill: #c7cbd3 !important;
  font-family: 'JetBrains Mono', 'SF Mono', Menlo, Monaco, 'Courier New', 'PingFang SC', sans-serif !important;
  font-size: 12px !important;
  text-shadow: none !important;
}

.markdown-preview.markdown-preview .mermaid .cluster-label,
.markdown-preview.markdown-preview .mermaid .cluster-label span,
.markdown-preview.markdown-preview .mermaid .cluster text {
  color: #8d929a !important;
  fill: #8d929a !important;
  font-size: 11px !important;
}

.markdown-preview.markdown-preview .mermaid .edgePath .path,
.markdown-preview.markdown-preview .mermaid .flowchart-link,
.markdown-preview.markdown-preview .mermaid path.relation {
  stroke: #7a8089 !important;
  stroke-width: 1.35px !important;
}

.markdown-preview.markdown-preview .mermaid marker,
.markdown-preview.markdown-preview .mermaid marker path,
.markdown-preview.markdown-preview .mermaid .arrowheadPath {
  fill: #7a8089 !important;
  stroke: #7a8089 !important;
}

.markdown-preview.markdown-preview .mermaid .edgeLabel,
.markdown-preview.markdown-preview .mermaid .edgeLabel p,
.markdown-preview.markdown-preview .mermaid .edgeLabel span {
  color: #a3a9b3 !important;
  background: #1e1f22 !important;
  font-size: 11px !important;
}

.markdown-preview.markdown-preview .mermaid .edgeLabel rect,
.markdown-preview.markdown-preview .mermaid .labelBkg {
  fill: #1e1f22 !important;
  background: #1e1f22 !important;
}
/* <<< codex-mermaid-idea-dark <<< */
```

## Markdown Preview Enhanced style.less

```less
/* JetBrains Darcula/New UI style for Markdown Preview Enhanced. */
:root {
  --jb-bg: #1e1f22;
  --jb-bg-soft: #202124;
  --jb-panel: #2b2d30;
  --jb-panel-soft: #26282d;
  --jb-border: #3c3f44;
  --jb-border-soft: #34373d;
  --jb-text: #bcbec4;
  --jb-text-strong: #dfe1e5;
  --jb-text-muted: #8d929a;
  --jb-blue: #548af7;
  --jb-yellow: #d6b46d;
  --jb-orange: #cc7832;
  --jb-green: #6aab73;
}

html,
body,
.mume,
.markdown-preview,
.markdown-preview.markdown-preview {
  background: var(--jb-bg) !important;
}

.markdown-preview.markdown-preview {
  box-sizing: border-box;
  max-width: 1120px;
  margin: 0 auto;
  padding: 24px 40px 48px;
  color: var(--jb-text) !important;
  font-family: -apple-system, BlinkMacSystemFont, 'Inter', 'SF Pro Text', 'Segoe UI', 'PingFang SC', 'Microsoft YaHei', sans-serif;
  font-size: 14px;
  line-height: 1.45;
  letter-spacing: 0;
}

.markdown-preview.markdown-preview * {
  text-shadow: none !important;
}

.markdown-preview.markdown-preview ::selection {
  color: #ffffff;
  background: rgba(84, 138, 247, 0.42);
}

.markdown-preview.markdown-preview h1,
.markdown-preview.markdown-preview h2,
.markdown-preview.markdown-preview h3,
.markdown-preview.markdown-preview h4,
.markdown-preview.markdown-preview h5,
.markdown-preview.markdown-preview h6 {
  color: var(--jb-text-strong) !important;
  font-weight: 680;
  line-height: 1.22;
  letter-spacing: 0;
}

.markdown-preview.markdown-preview h1 {
  margin: 0 0 20px;
  padding-bottom: 12px;
  border-bottom: 1px solid var(--jb-border-soft);
  font-size: 28px;
}

.markdown-preview.markdown-preview h2 {
  margin-top: 28px;
  margin-bottom: 14px;
  padding-bottom: 7px;
  border-bottom: 1px solid #30333a;
  font-size: 21px;
}

.markdown-preview.markdown-preview h3 {
  margin-top: 21px;
  margin-bottom: 10px;
  font-size: 17px;
}

.markdown-preview.markdown-preview h4 {
  margin-top: 17px;
  margin-bottom: 8px;
  font-size: 15px;
}

.markdown-preview.markdown-preview p,
.markdown-preview.markdown-preview li,
.markdown-preview.markdown-preview td {
  color: var(--jb-text) !important;
}

.markdown-preview.markdown-preview p {
  margin-top: 0;
  margin-bottom: 12px;
}

.markdown-preview.markdown-preview ul,
.markdown-preview.markdown-preview ol {
  margin-top: 6px;
  margin-bottom: 12px;
}

.markdown-preview.markdown-preview strong,
.markdown-preview.markdown-preview b {
  color: var(--jb-text-strong) !important;
  font-weight: 680;
}

.markdown-preview.markdown-preview a {
  color: var(--jb-blue) !important;
  text-decoration: none;
}

.markdown-preview.markdown-preview a:hover {
  color: #6ca0ff !important;
  text-decoration: underline;
}

.markdown-preview.markdown-preview blockquote {
  margin: 12px 0;
  padding: 8px 12px;
  color: #aeb4bd !important;
  border-left: 3px solid #5f7fbf;
  background: #24262b !important;
}

.markdown-preview.markdown-preview :not(pre) > code {
  padding: 1px 5px;
  border: 1px solid #3b3e45;
  border-radius: 4px;
  color: #d7dce2 !important;
  background: #2d3036 !important;
  font-family: 'JetBrains Mono', 'SF Mono', Menlo, Monaco, 'Courier New', monospace;
  font-size: 0.9em;
}

.markdown-preview.markdown-preview pre,
.markdown-preview.markdown-preview .code-chunk,
.markdown-preview.markdown-preview .md-fences {
  margin: 12px 0 16px;
  padding: 10px 13px !important;
  border: 1px solid var(--jb-border-soft) !important;
  border-radius: 5px !important;
  background: var(--jb-panel) !important;
  box-shadow: none !important;
}

.markdown-preview.markdown-preview pre code,
.markdown-preview.markdown-preview code[class*='language-'],
.markdown-preview.markdown-preview pre[class*='language-'] {
  color: #c9ccd3 !important;
  background: transparent !important;
  font-family: 'JetBrains Mono', 'SF Mono', Menlo, Monaco, 'Courier New', monospace;
  font-size: 13px;
  line-height: 1.38;
  letter-spacing: 0;
  text-shadow: none !important;
}

.markdown-preview.markdown-preview .hljs-keyword,
.markdown-preview.markdown-preview .hljs-selector-tag,
.markdown-preview.markdown-preview .hljs-literal,
.markdown-preview.markdown-preview .hljs-name,
.markdown-preview.markdown-preview .token.keyword,
.markdown-preview.markdown-preview .token.selector,
.markdown-preview.markdown-preview .token.important,
.markdown-preview.markdown-preview .token.atrule {
  color: var(--jb-orange) !important;
}

.markdown-preview.markdown-preview .hljs-string,
.markdown-preview.markdown-preview .hljs-regexp,
.markdown-preview.markdown-preview .hljs-symbol,
.markdown-preview.markdown-preview .hljs-bullet,
.markdown-preview.markdown-preview .token.string,
.markdown-preview.markdown-preview .token.char,
.markdown-preview.markdown-preview .token.regex,
.markdown-preview.markdown-preview .token.attr-value {
  color: var(--jb-green) !important;
}

.markdown-preview.markdown-preview .hljs-number,
.markdown-preview.markdown-preview .hljs-attr,
.markdown-preview.markdown-preview .hljs-variable,
.markdown-preview.markdown-preview .hljs-template-variable,
.markdown-preview.markdown-preview .token.number,
.markdown-preview.markdown-preview .token.boolean,
.markdown-preview.markdown-preview .token.constant,
.markdown-preview.markdown-preview .token.property {
  color: var(--jb-yellow) !important;
}

.markdown-preview.markdown-preview .hljs-comment,
.markdown-preview.markdown-preview .hljs-quote,
.markdown-preview.markdown-preview .token.comment,
.markdown-preview.markdown-preview .token.prolog,
.markdown-preview.markdown-preview .token.doctype,
.markdown-preview.markdown-preview .token.cdata {
  color: #7a8089 !important;
  font-style: italic;
}

.markdown-preview.markdown-preview .token.function,
.markdown-preview.markdown-preview .hljs-title,
.markdown-preview.markdown-preview .hljs-function {
  color: #ffc66d !important;
}

.markdown-preview.markdown-preview .token.operator,
.markdown-preview.markdown-preview .token.punctuation {
  color: #bcbec4 !important;
}

.markdown-preview.markdown-preview .code-active-line {
  background: rgba(84, 138, 247, 0.10) !important;
}

.markdown-preview.markdown-preview .code-line:hover {
  background: rgba(255, 255, 255, 0.03) !important;
}

.markdown-preview.markdown-preview table {
  display: block;
  width: 100%;
  overflow: auto;
  border-collapse: collapse;
  font-size: 13px;
  color: var(--jb-text) !important;
}

.markdown-preview.markdown-preview table th,
.markdown-preview.markdown-preview table td {
  padding: 6px 9px;
  border: 1px solid var(--jb-border) !important;
}

.markdown-preview.markdown-preview table th {
  color: var(--jb-text-strong) !important;
  background: #25272c !important;
  font-weight: 680;
}

.markdown-preview.markdown-preview table td {
  background: #1f2024 !important;
}

.markdown-preview.markdown-preview table tr:nth-child(2n) td {
  background: #222429 !important;
}

.markdown-preview.markdown-preview hr {
  border: 0;
  border-top: 1px solid var(--jb-border-soft);
}

.markdown-preview.markdown-preview mark {
  color: #111318;
  background: #d6b46d;
}

@media (max-width: 900px) {
  .markdown-preview.markdown-preview {
    padding: 20px 24px 42px;
  }

  .markdown-preview.markdown-preview h1 {
    font-size: 24px;
  }
}

/* >>> codex-mermaid-idea-dark >>> */
.markdown-preview.markdown-preview .mermaid,
.markdown-preview.markdown-preview pre.mermaid,
.markdown-preview.markdown-preview div.mermaid {
  box-sizing: border-box;
  margin: 14px 0 20px;
  padding: 12px 14px;
  overflow: auto;
  border: 1px solid #34373d !important;
  border-radius: 6px;
  background: #1e1f22 !important;
}

.markdown-preview.markdown-preview .mermaid svg {
  display: block;
  max-width: 100%;
  height: auto;
  margin: 0 auto;
  background: transparent !important;
}

.markdown-preview.markdown-preview .mermaid .node rect,
.markdown-preview.markdown-preview .mermaid .node polygon,
.markdown-preview.markdown-preview .mermaid .node circle,
.markdown-preview.markdown-preview .mermaid .node ellipse {
  fill: #25272c !important;
  stroke: #6f849c !important;
  stroke-width: 1.25px !important;
}

.markdown-preview.markdown-preview .mermaid .cluster rect {
  fill: #202124 !important;
  stroke: #3c3f44 !important;
  stroke-width: 1px !important;
}

.markdown-preview.markdown-preview .mermaid .label,
.markdown-preview.markdown-preview .mermaid .nodeLabel,
.markdown-preview.markdown-preview .mermaid .cluster-label,
.markdown-preview.markdown-preview .mermaid .edgeLabel,
.markdown-preview.markdown-preview .mermaid text,
.markdown-preview.markdown-preview .mermaid span {
  color: #c7cbd3 !important;
  fill: #c7cbd3 !important;
  font-family: 'JetBrains Mono', 'SF Mono', Menlo, Monaco, 'Courier New', 'PingFang SC', sans-serif !important;
  font-size: 12px !important;
  text-shadow: none !important;
}

.markdown-preview.markdown-preview .mermaid .cluster-label,
.markdown-preview.markdown-preview .mermaid .cluster-label span,
.markdown-preview.markdown-preview .mermaid .cluster text {
  color: #8d929a !important;
  fill: #8d929a !important;
  font-size: 11px !important;
}

.markdown-preview.markdown-preview .mermaid .edgePath .path,
.markdown-preview.markdown-preview .mermaid .flowchart-link,
.markdown-preview.markdown-preview .mermaid path.relation {
  stroke: #7a8089 !important;
  stroke-width: 1.35px !important;
}

.markdown-preview.markdown-preview .mermaid marker,
.markdown-preview.markdown-preview .mermaid marker path,
.markdown-preview.markdown-preview .mermaid .arrowheadPath {
  fill: #7a8089 !important;
  stroke: #7a8089 !important;
}

.markdown-preview.markdown-preview .mermaid .edgeLabel,
.markdown-preview.markdown-preview .mermaid .edgeLabel p,
.markdown-preview.markdown-preview .mermaid .edgeLabel span {
  color: #a3a9b3 !important;
  background: #1e1f22 !important;
  font-size: 11px !important;
}

.markdown-preview.markdown-preview .mermaid .edgeLabel rect,
.markdown-preview.markdown-preview .mermaid .labelBkg {
  fill: #1e1f22 !important;
  background: #1e1f22 !important;
}
/* <<< codex-mermaid-idea-dark <<< */
```

## Compact IDEA Terminal Prompt Block

```zsh
# >>> codex-compact-idea-prompt >>>
# Compact prompt for VS Code/IDEA-like terminals: current folder and Git branch inline.
autoload -Uz add-zsh-hook

_codex_compact_git_branch() {
  command git symbolic-ref --quiet --short HEAD 2>/dev/null || command git rev-parse --short HEAD 2>/dev/null
}

_codex_compact_git_status() {
  command git rev-parse --is-inside-work-tree >/dev/null 2>&1 || return
  local branch
  branch="$(_codex_compact_git_branch)"
  [[ -n "$branch" ]] || return
  print -r -- "%F{#7A7F87}git:(%f%F{#73A978}$branch%f%F{#7A7F87})%f"
}

_codex_compact_prompt_precmd() {
  setopt PROMPT_SUBST
  PROMPT='%F{#6A9FBF}%1~%f $(_codex_compact_git_status) %F{#8C8F96}›%f '
  RPROMPT=''
}

# Disable theme precmd hooks such as agnoster after Oh My Zsh has loaded.
precmd_functions=(${precmd_functions:#prompt_*})
add-zsh-hook precmd _codex_compact_prompt_precmd
_codex_compact_prompt_precmd
# <<< codex-compact-idea-prompt <<<
```

---
name: clip
description: Copy text to the user's local clipboard over SSH and tmux via OSC 52. Use when the user types /clip or asks to copy something to their clipboard.
argument-hint: "[what to copy, default: the main text of your last reply]"
---

Copy text to the user's clipboard on the device they are connecting from (e.g. Termux on a phone).

What to copy:
- If `$ARGUMENTS` names something ("the reply", "the command", a file path), copy that.
- If `$ARGUMENTS` is empty, copy the main deliverable of your last reply (a drafted message, a command, a code block), not the commentary around it. Copy it verbatim, without code fences.

Steps:
1. Write the exact text to `clip.txt` in your scratchpad directory with a quoted heredoc (`<<'EOF'`) so nothing gets expanded. For a file the user named, use that path directly.
2. Send it:
   - Inside tmux (`$TMUX` set): `tmux load-buffer -w <file>`. This also leaves it in tmux's paste buffer.
   - Outside tmux: `printf '\033]52;c;%s\a' "$(base64 -w0 <file>)" > /dev/tty`
3. Reply in one or two lines: what was copied (first few words and length) and that it can be pasted now. If it doesn't show up, the user can paste inside tmux with `Ctrl-b ]`, or `cat` the file and select it by hand.

Do not print the copied text again in the reply.

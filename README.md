# hf-skills

Skill khusus produksi video motion-graphics + kinetic-typography (hyperframes engine).

## Isi

| Skill | Fungsi |
|---|---|
| `motion-video` | Toolchain + struktur proyek + kontrak render hyperframes (clip, timeline, GSAP) |
| `reels-typo` | Style guide kinetic-typography: background-type, komposisi serif+sans, palet, FX icon presets (13), word-timing pipeline, aspect-ratio layouts (9:16/4:5/1:1/16:9) |

## Install

```bash
git clone https://github.com/agungbharata/hf-skills.git /tmp/hf-skills
mkdir -p ~/.agents/skills ~/.claude/skills
cp -r /tmp/hf-skills/{motion-video,reels-typo} ~/.agents/skills/
for n in motion-video reels-typo; do
  [ -e ~/.claude/skills/$n ] || ln -s ~/.agents/skills/$n ~/.claude/skills/$n
done
```

Restart Claude Code setelah install.

## Update

```bash
cd ~/.agents/skills
# ... edit motion-video / reels-typo ...
# lalu sinkron ke repo:
cp -r ~/.agents/skills/{motion-video,reels-typo} /tmp/hf-sync/  # atau commit langsung dari clone
```

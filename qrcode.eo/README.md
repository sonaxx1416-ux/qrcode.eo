# qrcode.eo

Make QR codes from eo, like Python's `qrcode`. It's written entirely in eo (Reed-Solomon, masks and the PNG encoder), with no changes to the language.

```eo
import qrcode

var q = qrcode.QR("https://eolang.com")
q.save "qr.png"
q.show
```

`q.show` draws the code in the terminal, and a phone can scan it straight off the screen.

## Saving

| Path ends with | You get |
|---|---|
| `.png` (or anything else) | A PNG image |
| `.svg` | A vector image you can scale to any size |
| `.txt` | The code as text drawn with `█` |

`save` returns `true` on success, or `false` with the reason in `q.error`:

```eo
if not q.save("qr.png") {
    print q.error
}
```

## Options

Set them before `save` or `show`:

```eo
var q = qrcode.QR("WIFI:T:WPA;S:MyHome;P:12345678;;")
q.level = "H"          # error correction: L 7%, M 15% (default), Q 25%, H 30%
q.box = 16             # pixels per square in the PNG (default 10)
q.border = 2           # quiet zone in squares (default 4)
q.fill = "#d94a5c"     # square colour
q.back = "#fff8f0"     # background colour
q.version = 5          # force a version 1–10 (0 = smallest that fits)
q.mask = 3             # force a mask 0–7 (-1 = best)
q.save "wifi.png"
```

After saving, these fields are filled in:

| Field | Meaning |
|---|---|
| `q.used_version` | The version that was used |
| `q.used_mask` | The mask that was used |
| `q.size` | Number of squares per side |
| `q.matrix` | The squares as a list of rows of `true`/`false` |
| `q.dark(x, y)` | Whether one square is dark |

Other ways to get the output:

- `q.png()` returns the PNG bytes.
- `q.svg()` returns the SVG text.
- `q.text()` returns the text version.
- `q.terminal()` returns the coloured terminal version as a string.

There's also a shortcut: `qrcode.make("text").save "a.png"`.

## Limits

- It supports versions 1 to 10.
- At level L the most it fits is 271 bytes. At M it's 213. Arabic letters take 2 bytes each and emoji take 4.
- The PNG isn't compressed, so files are a bit bigger than usual (about 14 KB for a basic link).

## Testing

Every version, level and mask was read back with a real QR reader and matched the exact text. That includes Arabic, emoji, SVG and the terminal output.

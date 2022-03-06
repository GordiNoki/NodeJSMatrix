var symbols = Array(127 - 33).fill().map((_, i) => String.fromCharCode(i + 33))

function moveCursor(direction, amount) {
    var h = Buffer.from([0x1B, 0x5B]).toString("utf8")
    var directionToL = { "Up": "A", "Down": "B", "Right": "C", "Left": "D" }
    return h + amount + directionToL[direction]
}

function colored(text, foregroundId, backgroundId = null, nextForeground = null, nextBackground = null) {
    var h = Buffer.from([0x1B, 0x5B]).toString("utf8")
    var t = "";
    if(nextForeground != null) t += h + `38;5;${nextForeground}m`;
    if(nextBackground != null) t += h + `48;5;${nextBackground}m`;
    return (backgroundId != null ? (h + `48;5;${backgroundId}m`) : "") + h + `38;5;${foregroundId}m` + text + h + "0m" + t
}

function PosToIndex(x, y) {
    return y * process.stdout.columns + x
}

var bkg = Array(process.stdout.columns * process.stdout.rows).fill(" ").map(() => { return symbols[Math.floor(symbols.length * Math.random())] })
var screen = Array(process.stdout.columns * process.stdout.rows).fill(" ").map(() => { return symbols[Math.floor(symbols.length * Math.random())] })

class FallingChar {
    constructor() {
        this.y = Math.floor(process.stdout.rows * Math.random())
        this.x = Math.floor(process.stdout.columns * Math.random())
        this.length = 5+Math.floor(Math.random()*(process.stdout.rows-5))
        this.colorPower = [46, 40, 34, 28, 22]
    }
    draw() {
        for(var t = 0; t < this.length; t++) {
            if(this.y - t < 0 || this.y - t >= process.stdout.rows) continue;
            var c = this.length - t <= 4 ? this.colorPower[4 - (this.length - t)] : this.colorPower[0]
            screen[PosToIndex(this.x, this.y - t)] = colored(screen[PosToIndex(this.x, this.y - t)], c, 233, 0, 233)
        }
    }
    reSpawn() {
        this.y = 0
        this.x = Math.floor(process.stdout.columns * Math.random())
        this.length = 5+Math.floor(Math.random()*(process.stdout.rows-5))
    }
    update() {
        this.y += 1
        if(this.y - this.length >= process.stdout.rows) this.reSpawn()
    }
}

var fallings = Array(Math.floor(process.stdout.columns / 2)).fill().map(() => new FallingChar())

setInterval(() => {
    fallings.forEach(f => f.draw())
    process._rawDebug(Buffer.from([0x1B]).toString("utf8")+"c"+colored(screen.join(""), 0, 233 )+moveCursor("Up", 1));
    screen = screen.map((_, i) => bkg[i])
    fallings.forEach(f => f.update())
}, 100)

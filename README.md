# blog
<!doctype html>
<html>
<head>
  <meta charset="utf-8" />
  <title>Random L-System Plant</title>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/p5.js/1.6.0/p5.min.js"></script>
  <style>
    body { margin:0; background:#111; }
    h1 {
      position: absolute; top: 10px; left: 10px;
      color: #fff; font-family: sans-serif;
      font-size: 14px; opacity: 0.6;
    }
  </style>
</head>
<body>
  <h1>Refresh to grow a new plant 🌱</h1>
  <script>
    // --- Random L-system plant ---
    let axiom, rules, sentence;
    let angleDeg, len, iterations;

    function randomRule() {
      // picks a random rule pattern for X or F
      let patterns = [
        "F-[[X]+X]+F[+FX]-X",
        "F[+X][-X]FX",
        "F[+X]F[-X]+X",
        "F[-X]F[+X]+X",
        "FF[+X][-X]X",
      ];
      return random(patterns);
    }

    function setupRandomSystem() {
      axiom = "X";
      rules = [
        { a: "X", b: randomRule() },
        { a: "F", b: (random() < 0.5 ? "FF" : "F") }
      ];
      angleDeg = random(15, 35);          // degrees
      len = random(50, 120);              // initial length
      iterations = int(random(4, 7));     // 4–6 iterations
    }

    function applyRules(c) {
      for (let r of rules) {
        if (c === r.a) return r.b;
      }
      return c;
    }

    function generate() {
      sentence = axiom;
      for (let i = 0; i < iterations; i++) {
        let next = "";
        for (let ch of sentence) {
          next += applyRules(ch);
        }
        sentence = next;
      }
    }

    function setup() {
      createCanvas(windowWidth, windowHeight);
      angleMode(DEGREES);
      stroke(100, 220, 100);

      setupRandomSystem();
      generate();
      noLoop();
    }

    function draw() {
      background(17);
      translate(width/2, height);
      rotate(-90); // point upward initially

      let stack = [];
      let currentLen = len;
      let depth = 0;

      for (let i = 0; i < sentence.length; i++) {
        let c = sentence[i];
        switch (c) {
          case 'F':
            strokeWeight(max(0.25, 3 - depth * 0.3));
            line(0,0,currentLen,0);
            translate(currentLen,0);
            break;
          case 'f':
            translate(currentLen,0);
            break;
          case '+':
            rotate(angleDeg);
            break;
          case '-':
            rotate(-angleDeg);
            break;
          case '[':
            push();
            stack.push({depth: depth, len: currentLen});
            depth++;
            currentLen *= 0.66;
            break;
          case ']':
            if (stack.length>0) {
              let s=stack.pop();
              pop();
              depth=s.depth;
              currentLen=s.len;
            } else {
              pop();
            }
            break;
          case 'X':
          default:
            // do nothing
            break;
        }
      }
    }

    function windowResized() {
      resizeCanvas(windowWidth, windowHeight);
      redraw();
    }
  </script>
</body>
</html>

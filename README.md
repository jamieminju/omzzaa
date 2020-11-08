# omzzaa
let movers = [];

// Liquid
let liquid;

function setup() {
  createCanvas(640, 360);
  reset();
  liquid = new Liquid(0, height / 2, width, height / 2, 0.1);
}

function draw() {
  background(230);
  liquid.display();

  for (let i = 0; i < movers.length; i++) {
    if (liquid.contains(movers[i])) {
      let dragForce = liquid.calculateDrag(movers[i]);
      movers[i].applyForce(dragForce);
    }
    let gravity = createVector(0, 0.1 * movers[i].mass);
    movers[i].applyForce(gravity);
    
    movers[i].update();
    movers[i].display();
    movers[i].checkEdges();
  }

}

//function mousePressed() {
  //movers[i] = new Mover(mouseX, mouseY, random(2, 4));
//}

function mousePressed() {
  reset();
}
function reset() {
  for (let i = 0; i < 9; i++) {
    movers[i] = new Mover(random(0.5, 3), 40 + i * 70, 0);
  }
}

let Liquid = function(x, y, w, h, c) {
  this.x = x;
  this.y = y;
  this.w = w;
  this.h = h;
  this.c = c;
};

Liquid.prototype.contains = function(m) {
  let l = m.position;
  return l.x > this.x && l.x < this.x + this.w &&
         l.y > this.y && l.y < this.y + this.h;
};

Liquid.prototype.calculateDrag = function(m) {
  let speed = m.velocity.mag();
  let dragMagnitude = this.c * speed * speed;

  let dragForce = m.velocity.copy();
  dragForce.mult(-1);
  dragForce.normalize();
  dragForce.mult(dragMagnitude);
  return dragForce;
};

Liquid.prototype.display = function() {
  noStroke();
  fill(180, 200, 255);
  rect(this.x, this.y, this.w, this.h);
};

function Mover(m, x, y) {
  this.mass = m;
  this.position = createVector(x, y);
  this.velocity = createVector(0, 0);
  this.acceleration = createVector(0, 0);
}

Mover.prototype.applyForce = function(force) {
  let f = p5.Vector.div(force, this.mass);
  this.acceleration.add(f);
};

Mover.prototype.update = function() {
  this.velocity.add(this.acceleration);
  this.position.add(this.velocity);
  this.acceleration.mult(0);
};

Mover.prototype.display = function() {
  stroke(0);
  strokeWeight(2);
  fill(255,127);
  ellipse(this.position.x, this.position.y, this.mass * 16, this.mass * 16);
};

Mover.prototype.checkEdges = function() {
  if (this.position.y > (height - this.mass * 8)) {
    this.velocity.y *= -0.9;
    this.position.y = (height - this.mass * 8);
  }
};

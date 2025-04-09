# Cellular-Automata
My take on Conway's Game of Life with multiple shapes and colors being used to depict the states of life and death

In a traditional implementation of Conway's Game of Life we tend to use only one shape that is square and 2 colors - black for depicting if the cell is alive and white if it is dead. In this example I have tweaked a few things. I have also created a class to represent a cell which has allowed me to only used 1 copy of array and use 2 different variables to store current and next state.

1] I have used 2 different shapes - Square represents if the cell is alive and circle means that the cell is dead. 

2] I have used 4 colors. Cell state changes are represented by following colors:
- 0 to 1 - Green
- 1 to 0 - Red
- 1 to 1 - Blue
- 0 to 0 - Blue

[P5.js Sketch Link](https://editor.p5js.org/map.gamedev.ubi/sketches/LVGO0g8vz)

## Code:
```
let canvas_width;
let canvas_height;
let columns_to_use, rows_to_use;
let cell_width = 20;
let board = [];

let draw_cooldown_time = 0;

function setup() {
  // rectMode(CENTER)
  frameRate(60)
  canvas_width = screen.width / 2.1;
  canvas_height = screen.height / 1.7;
  createCanvas(canvas_width, canvas_height);
  init_board();
}

function draw() {
  if(draw_cooldown_time <= 0){
    background(220);
    draw_cooldown_time = 0.1;
    draw_board();
    compute_board();  
  }
  else{
    draw_cooldown_time -= 0.1;
  }
}

function init_board(){
  columns_to_use = floor(canvas_width / cell_width);
  rows_to_use = floor(canvas_height / cell_width);
  board = create_arr(columns_to_use, rows_to_use);
}

function compute_board(){
  let x_index, y_index;
  
  for(let i = 0; i < columns_to_use; i++){
    for(let j = 0; j < rows_to_use; j++){
      let neighbour_sum = 0;
      for(let k = -1; k <= 1; k++){
        for(let l = -1; l <= 1; l++){
          x_index = i + k;
          y_index = j + l;
          
          if(x_index < 0){
            x_index = columns_to_use - 1;
          }
          else if(x_index >= columns_to_use){
            x_index = 0;
          }
          
          if(y_index < 0){
            y_index = rows_to_use - 1;
          }
          else if(y_index >= rows_to_use){
            y_index = 0;
          }
          
          neighbour_sum += board[x_index][y_index].current_state;
        }
      }
      
      neighbour_sum -= board[i][j].current_state;  
      board[i][j].calculate_next_state(neighbour_sum);
    }
  }
}

function draw_board(){
  for(let i = 0; i < columns_to_use; i++){
    for(let j = 0; j < rows_to_use; j++){
      board[i][j].draw_cell();
    }
  }
}

function create_arr(columns, rows){
  let arr = new Array(this.columns)
  for(let i = 0; i < columns; i++){
    arr[i] = new Array(rows);
    for(let j = 0; j < rows; j++){
      arr[i][j] = new Cell(i, j);
    }
  }
  return arr;
}

class Cell {
  constructor(column_index, row_index){
    this.current_state = floor(random(2));
    this.next_state = this.current_state;
    
    this.xPosition = column_index * cell_width;
    this.yPosition = row_index * cell_width;

    this.update_color();
  }
  
  calculate_next_state(neighbour_sum){
      if(this.current_state == 1 && neighbour_sum < 2){
        this.next_state = 0;
      }
      else if(this.current_state == 1 && neighbour_sum > 3){
        this.next_state = 0;
      }
      else if(this.current_state == 0 && neighbour_sum == 3){
        this.next_state = 1;
      }
      else{
        this.next_state = this.current_state;
      }
    
      this.update_color();
  }
  
  update_state(){
    let temp_state = this.current_state;
    this.current_state = this.next_state;
    this.next_state = temp_state;
  }
  
  update_color(){
    
    if(this.current_state == 1 && this.next_state == 0){
      this.colorR = 255;
      this.colorG = 0;
      this.colorB = 0;
    }
    else if(this.current_state == 0 && this.next_state == 1){
      this.colorG = 255;
      this.colorR = 0;
      this.colorB = 0;
    }
    else if(this.current_state == 0 && this.next_state == 0){
      this.colorB = 50;
      this.colorG = 0;
      this.colorR = 50;
    }
    else{
      this.colorR = 0;
      this.colorG = 250;
      this.colorB = 250;
    }
  }
  
  draw_cell(){
    this.update_state();
    fill(this.colorR, this.colorG, this.colorB);
    // fill(255 - 255 * this.current_state);
    stroke(0);
    if(this.current_state == 1){
      // circle(this.xPosition, this.yPosition, cell_width);
      square(this.xPosition, this.yPosition, cell_width);
    }
    else{
      
      circle(this.xPosition, this.yPosition, cell_width);
      // square(this.xPosition, this.yPosition, cell_width);
    }
  }
}

```

---
layout: post
title:  "Introducing Tale"
author: "Chester"
---

# getting the diagonals for a array point

vamos utilizar a matriz a baixo para desenvolver 
```javascript
  var matriz = [];
  matriz[0] = [0,1,2,3,4,5]
  matriz[1] = [10,11,12,13,14,15]
  matriz[2] = [20,21,22,23,24,25]
  matriz[3] = [30,31,32,33,34,35]
  matriz[4] = [40,41,42,43,44,45]
  matriz[5] = [50,51,52,53,54,55]
```
para pegarmos as diagonais do numero 33 por exemplo
```javascript
function setCross(x, y) {
  var diagonals = [];
  var selected = [x,y]
  for(var y in matriz) {
    for(var x in matriz[y]) {

      var slope = Math.abs(x - selected[0])/Math.abs(y - selected[1]);

      if(slope == 1) {
        diagonals.push([x,y]);
      }
    }
  }
  
  for(var x in diagonals) {
    $('.ponto[x="'+diagonals[x][0]+'"][y="'+diagonals[x][1]+'"]').addClass('active')
    
  }
}
```

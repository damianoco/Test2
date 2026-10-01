<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
<title>Vector addition</title>
<style>
:root{--bg:#f6f8fb;--panel:#fff;--ink:#14213d;--muted:#5b6478;--grid:#dbe2ee;--axis:#8b96ab;--a:#0f766e;--b:#6d28d9;--s:#be123c;
box-sizing:border-box;padding-top:env(safe-area-inset-top,0px);padding-bottom:env(safe-area-inset-bottom,0px)}
@media (prefers-color-scheme:dark){:root:not([data-theme="light"]){--bg:#0e1420;--panel:#172033;--ink:#e8edf7;--muted:#9aa5bd;--grid:#26324a;--axis:#5d6b88;--a:#2dd4bf;--b:#a78bfa;--s:#fb7185}}
:root[data-theme="dark"]{--bg:#0e1420;--panel:#172033;--ink:#e8edf7;--muted:#9aa5bd;--grid:#26324a;--axis:#5d6b88;--a:#2dd4bf;--b:#a78bfa;--s:#fb7185}
*{box-sizing:border-box}
html{scroll-padding-top:env(safe-area-inset-top,0px)}
body{margin:0;background:var(--bg);color:var(--ink);font:16px/1.5 system-ui,-apple-system,"Segoe UI",Roboto,sans-serif}
main{max-width:640px;margin:0 auto;padding:20px 16px 32px}
h1{font-size:1.5rem;margin:0 0 4px}
p.lead{margin:0 0 16px;color:var(--muted)}
.panel{background:var(--panel);border-radius:14px;padding:14px;margin-bottom:14px;border:1px solid var(--grid)}
.row{display:flex;align-items:center;gap:8px;flex-wrap:wrap;margin-bottom:10px}
.row:last-child{margin-bottom:0}
.name{font-weight:700;width:2.2rem}
.a{color:var(--a)}.b{color:var(--b)}.s{color:var(--s)}
label{display:flex;align-items:center;gap:6px;color:var(--muted)}
input{width:5.5rem;font:inherit;font-size:1rem;padding:8px;border-radius:8px;border:1px solid var(--axis);background:var(--bg);color:var(--ink)}
input:focus-visible{outline:3px solid var(--b);outline-offset:1px}
.res{font-size:1.15rem;font-weight:600}
.meta{color:var(--muted);margin-top:4px}
svg{display:block;width:100%;height:auto;max-height:70vh;background:var(--panel);border:1px solid var(--grid);border-radius:14px}
.legend{display:flex;gap:14px;flex-wrap:wrap;margin-top:10px;color:var(--muted);font-size:.9rem}
.legend span::before{content:"";display:inline-block;width:18px;height:0;border-top:3px solid currentColor;margin-right:6px;vertical-align:middle}
.legend .a::before,.legend .b::before,.legend .s::before{border-top-color:currentColor}
.legend i{font-style:normal}
.legend .d::before{border-top-style:dashed;border-top-width:2px}
</style>
</head>
<body>
<main>
<h1>Vector addition</h1>
<p class="lead">Enter two 2D vectors. The sum and the graph update as you type.</p>

<div class="panel">
  <div class="row"><span class="name a">A =</span>
    <label>x <input id="ax" type="number" inputmode="decimal" step="any" value="3"></label>
    <label>y <input id="ay" type="number" inputmode="decimal" step="any" value="2"></label></div>
  <div class="row"><span class="name b">B =</span>
    <label>x <input id="bx" type="number" inputmode="decimal" step="any" value="-1"></label>
    <label>y <input id="by" type="number" inputmode="decimal" step="any" value="4"></label></div>
</div>

<div class="panel" aria-live="polite">
  <div class="res s" id="sum"></div>
  <div class="meta" id="detail"></div>
</div>

<svg id="plot" viewBox="0 0 400 400" role="img" aria-label="Graph of vectors A, B and their sum">
  <defs>
    <marker id="mA" viewBox="0 0 10 10" refX="8" refY="5" markerWidth="7" markerHeight="7" orient="auto"><path d="M0 0L10 5L0 10z" fill="var(--a)"/></marker>
    <marker id="mB" viewBox="0 0 10 10" refX="8" refY="5" markerWidth="7" markerHeight="7" orient="auto"><path d="M0 0L10 5L0 10z" fill="var(--b)"/></marker>
    <marker id="mS" viewBox="0 0 10 10" refX="8" refY="5" markerWidth="7" markerHeight="7" orient="auto"><path d="M0 0L10 5L0 10z" fill="var(--s)"/></marker>
  </defs>
  <g id="g"></g>
</svg>
<div class="legend">
  <span class="a">A</span><span class="b">B (placed at tip of A)</span><span class="s">A + B</span><span class="d" style="color:var(--muted)">completes the parallelogram</span>
</div>
</main>
<script>
(function(){
  var ids=["ax","ay","bx","by"],el={};
  ids.forEach(function(i){el[i]=document.getElementById(i);el[i].addEventListener("input",draw);});
  var g=document.getElementById("g"),NS="http://www.w3.org/2000/svg";
  function fmt(n){return String(Math.round(n*1000)/1000);}
  function val(i){var v=parseFloat(el[i].value);return isFinite(v)?v:0;}
  function add(tag,attrs,text){var e=document.createElementNS(NS,tag);for(var k in attrs)e.setAttribute(k,attrs[k]);if(text!==undefined)e.textContent=text;g.appendChild(e);return e;}
  function niceStep(r){var raw=r/4,p=Math.pow(10,Math.floor(Math.log10(raw))),f=raw/p;return (f<=1?1:f<=2?2:f<=5?5:10)*p;}
  function draw(){
    var ax=val("ax"),ay=val("ay"),bx=val("bx"),by=val("by"),sx=ax+bx,sy=ay+by;
    document.getElementById("sum").textContent="A + B = ("+fmt(sx)+", "+fmt(sy)+")";
    var mag=Math.hypot(sx,sy),ang=mag===0?"undefined":fmt(Math.atan2(sy,sx)*180/Math.PI)+"°";
    document.getElementById("detail").textContent="Length "+fmt(mag)+", angle "+ang+" from the positive x-axis";
    var m=Math.max(1,Math.abs(ax),Math.abs(ay),Math.abs(bx),Math.abs(by),Math.abs(sx),Math.abs(sy),Math.abs(ax+bx),Math.abs(ay+by));
    var step=niceStep(m*1.2),R=Math.ceil(m*1.15/step)*step;
    var c=200,k=170/R;
    function X(x){return c+x*k;}function Y(y){return c-y*k;}
    g.textContent="";
    for(var t=-R;t<=R+1e-9;t+=step){
      var tt=Math.round(t/step)*step;
      add("line",{x1:X(tt),y1:Y(-R),x2:X(tt),y2:Y(R),stroke:"var(--grid)","stroke-width":1});
      add("line",{x1:X(-R),y1:Y(tt),x2:X(R),y2:Y(tt),stroke:"var(--grid)","stroke-width":1});
      if(Math.abs(tt)>1e-9){
        add("text",{x:X(tt),y:Y(0)+13,"text-anchor":"middle","font-size":10,fill:"var(--muted)"},fmt(tt));
        add("text",{x:X(0)-5,y:Y(tt)+3,"text-anchor":"end","font-size":10,fill:"var(--muted)"},fmt(tt));
      }
    }
    add("line",{x1:X(-R),y1:Y(0),x2:X(R),y2:Y(0),stroke:"var(--axis)","stroke-width":1.5});
    add("line",{x1:X(0),y1:Y(-R),x2:X(0),y2:Y(R),stroke:"var(--axis)","stroke-width":1.5});
    function vec(x1,y1,x2,y2,col,mk,dash){
      var a={x1:X(x1),y1:Y(y1),x2:X(x2),y2:Y(y2),stroke:"var(--"+col+")","stroke-width":dash?1.5:3,"stroke-linecap":"round"};
      if(dash){a["stroke-dasharray"]="5 4";a.opacity=.7;}else a["marker-end"]="url(#"+mk+")";
      add("line",a);
    }
    vec(bx,by,sx,sy,"a",null,true);   /* A copied to tip of B */
    vec(ax,ay,sx,sy,"b","mB",false);   /* B at tip of A */
    vec(0,0,bx,by,"b","mB",true);      /* B from origin, dashed */
    vec(0,0,ax,ay,"a","mA",false);
    vec(0,0,sx,sy,"s","mS",false);
    add("circle",{cx:X(0),cy:Y(0),r:3.5,fill:"var(--ink)"});
    add("text",{x:X(ax/2)+6,y:Y(ay/2)-6,"font-size":13,"font-weight":700,fill:"var(--a)"},"A");
    add("text",{x:X(ax+bx/2)+6,y:Y(ay+by/2)-6,"font-size":13,"font-weight":700,fill:"var(--b)"},"B");
    add("text",{x:X(sx/2)-14,y:Y(sy/2)+16,"font-size":13,"font-weight":700,fill:"var(--s)"},"A+B");
  }
  draw();
})();
</script>
</body>
</html>


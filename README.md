# Somnath-
Marble&amp; grenite 
<!DOCTYPE html>
<html lang="gu">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1.0">

<title>માપ સોફ્ટવેર - Granite Marble</title>

<style>
*{box-sizing:border-box}

body{
 margin:0;
 background:#f3f6fa;
 font-family:Arial,"Noto Sans Gujarati",sans-serif;
 color:#1d2939;
}

.app{
 max-width:900px;
 margin:auto;
 padding:15px;
}

.header{
 background:white;
 border-radius:20px;
 padding:18px;
 margin-bottom:14px;
 box-shadow:0 3px 15px #00000010;
}

.header h1{
 margin:0;
 font-size:25px;
}

.header p{
 margin:6px 0 0;
 color:#667085;
}

.toolbar{
 background:white;
 border-radius:18px;
 padding:14px;
 display:flex;
 gap:10px;
 flex-wrap:wrap;
 margin-bottom:14px;
 box-shadow:0 3px 15px #00000010;
}

button{
 border:0;
 border-radius:12px;
 padding:12px 17px;
 font-size:16px;
 cursor:pointer;
}

.add{
 background:#146ef5;
 color:white;
}

.save{
 background:#198754;
 color:white;
}

.clear{
 background:#e5484d;
 color:white;
}

.print{
 background:#344054;
 color:white;
}

.search{
 width:100%;
 padding:14px;
 border:1px solid #d0d5dd;
 border-radius:12px;
 font-size:17px;
 outline:none;
 margin-bottom:14px;
}

.table{
 background:white;
 border-radius:18px;
 overflow:hidden;
 box-shadow:0 3px 15px #00000010;
}

.row{
 display:grid;
 grid-template-columns:1fr 1fr 1fr 75px;
 gap:8px;
 padding:10px 12px;
 border-bottom:1px solid #eaecf0;
 align-items:center;
}

.head{
 background:#f8fafc;
 font-weight:bold;
}

input{
 width:100%;
 padding:13px 10px;
 border:1px solid #d0d5dd;
 border-radius:10px;
 font-size:19px;
 outline:none;
}

input:focus{
 border:2px solid #146ef5;
}

.del{
 background:#fee4e2;
 color:#b42318;
 padding:10px;
}

.result{
 margin-top:14px;
 background:white;
 border-radius:18px;
 padding:15px;
 box-shadow:0 3px 15px #00000010;
}

.result h2{
 margin:0 0 10px;
 font-size:20px;
}

.measure{
 display:flex;
 justify-content:space-between;
 align-items:center;
 padding:13px 8px;
 border-bottom:1px solid #eaecf0;
 font-size:22px;
 font-weight:bold;
}

.qty{
 font-size:16px;
 color:#667085;
}

.total{
 margin-top:15px;
 padding:15px;
 background:#f2f4f7;
 border-radius:12px;
 font-size:19px;
 font-weight:bold;
}

.empty{
 text-align:center;
 padding:45px 10px;
 color:#98a2b3;
 font-size:18px;
}

.status{
 margin-top:10px;
 color:#667085;
 font-size:14px;
}

@media(max-width:600px){

 .app{
  padding:9px;
 }

 .header h1{
  font-size:22px;
 }

 .row{
  grid-template-columns:1fr 1fr 1fr 45px;
  padding:8px;
  gap:5px;
 }

 input{
  padding:11px 5px;
  font-size:17px;
 }

 .del{
  padding:9px 5px;
 }

 .measure{
  font-size:20px;
 }
}

@media print{

 body{
  background:white;
 }

 .toolbar,
 .entryTable,
 .search,
 .status{
  display:none!important;
 }

 .result{
  box-shadow:none;
 }
}
</style>
</head>

<body>

<div class="app">

 <div class="header">
   <h1>📏 માપ સોફ્ટવેર</h1>
   <p>Granite • Marble</p>
 </div>

 <div class="toolbar">

   <button class="add" onclick="addRow()">
     ＋ ઉમેરો
   </button>

   <button class="save" onclick="saveData()">
     💾 સાચવો
   </button>

   <button class="clear" onclick="clearAll()">
     🗑 ખાલી કરો
   </button>

   <button class="print" onclick="window.print()">
     🖨 છાપો
   </button>

 </div>

 <input
   id="search"
   class="search"
   type="text"
   inputmode="numeric"
   placeholder="🔎 માપ શોધો..."
   oninput="showResult()"
 >

 <div class="table entryTable">

   <div class="row head">
     <div>મોટું માપ</div>
     <div>નાનું માપ</div>
     <div>જથ્થો</div>
     <div></div>
   </div>

   <div id="rows"></div>

 </div>

 <div class="result">

   <h2>📋 માપ સૂચિ</h2>

   <div id="resultList"></div>

   <div class="total" id="total">
     કુલ જથ્થો: 0
   </div>

 </div>

 <div class="status" id="status">
   ડેટા ફોનમાં આપમેળે સાચવાય છે.
 </div>

</div>

<script>

/* =====================================================
   GRANITE / MARBLE MEASUREMENT SOFTWARE
   LocalStorage Based
   ===================================================== */

const STORAGE_KEY = "granite_marble_measurement_v1";

let rows = [];
let nextId = 1;


/* ---------- SAFE LOAD ---------- */

function loadData(){

 try{

   const saved = localStorage.getItem(STORAGE_KEY);

   if(!saved){
     rows = [];
     addRow(false);
     return;
   }

   const parsed = JSON.parse(saved);

   if(!Array.isArray(parsed)){
     rows = [];
     addRow(false);
     return;
   }

   rows = parsed.map(x => ({
     id:Number(x.id) || nextId++,
     big:x.big ?? "",
     small:x.small ?? "",
     qty:x.qty ?? "",
     order:Number(x.order) || 0
   }));

   if(rows.length === 0){
     addRow(false);
   }

   nextId =
     Math.max(
       0,
       ...rows.map(x => Number(x.id) || 0)
     ) + 1;

   render();

 }catch(error){

   console.log("Local data error:",error);

   rows = [];

   localStorage.removeItem(STORAGE_KEY);

   addRow(false);
 }
}


/* ---------- SAVE ---------- */

function saveData(){

 try{

   localStorage.setItem(
     STORAGE_KEY,
     JSON.stringify(rows)
   );

   document.getElementById("status").innerText =
     "✓ ડેટા સાચવાઈ ગયો";

 }catch(error){

   document.getElementById("status").innerText =
     "ડેટા save થઈ શક્યો નથી.";
 }
}


/* ---------- ADD ROW ---------- */

function addRow(focus=true){

 rows.push({
   id:nextId++,
   big:"",
   small:"",
   qty:"",
   order:Date.now()+Math.random()
 });

 render();

 saveData();

 if(focus){

   setTimeout(()=>{

     const inputs =
       document.querySelectorAll(".measurement-row");

     if(inputs.length){

       const last =
         inputs[inputs.length-1]
         .querySelector("input");

       if(last) last.focus();
     }

   },50);
 }
}


/* ---------- DELETE ---------- */

function deleteRow(id){

 rows = rows.filter(x => x.id !== id);

 if(rows.length === 0){
   addRow(false);
 }

 saveData();

 render();
}


/* ---------- UPDATE ---------- */

function updateRow(id,field,value){

 const row =
   rows.find(x => x.id === id);

 if(!row) return;

 /*
   Numbers are stored as numbers.
   Empty field remains empty.
 */

 if(value === ""){
   row[field] = "";
 }else{

   const number = Number(value);

   if(Number.isFinite(number)){
     row[field] = number;
   }
 }

 saveData();

 showResult();
}


/* ---------- SORT ---------- */

function sortedRows(){

 return [...rows].sort((a,b)=>{

   const bigA =
     a.big === "" ? -Infinity : Number(a.big);

   const bigB =
     b.big === "" ? -Infinity : Number(b.big);

   /* મોટું માપ descending */

   if(bigA !== bigB){
     return bigB - bigA;
   }

   const smallA =
     a.small === "" ? -Infinity : Number(a.small);

   const smallB =
     b.small === "" ? -Infinity : Number(b.small);

   /* નાનું માપ descending */

   if(smallA !== smallB){
     return smallB - smallA;
   }

   /* Same measurement = original order */

   return a.order - b.order;
 });
}


/* ---------- RENDER INPUTS ---------- */

function render(){

 const box =
   document.getElementById("rows");

 box.innerHTML = "";

 rows.forEach((row,index)=>{

   const div =
     document.createElement("div");

   div.className =
     "row measurement-row";

   div.innerHTML = `

     <input
       type="number"
       min="0"
       inputmode="decimal"
       placeholder="મોટું"
       value="${safeValue(row.big)}"
       data-id="${row.id}"
       data-field="big"
     >

     <input
       type="number"
       min="0"
       inputmode="decimal"
       placeholder="નાનું"
       value="${safeValue(row.small)}"
       data-id="${row.id}"
       data-field="small"
     >

     <input
       type="number"
       min="0"
       inputmode="decimal"
       placeholder="જથ્થો"
       value="${safeValue(row.qty)}"
       data-id="${row.id}"
       data-field="qty"
     >

     <button
       class="del"
       onclick="deleteRow(${row.id})">
       ✕
     </button>

   `;

   box.appendChild(div);
 });

 showResult();
}


/* ---------- SAFE HTML VALUE ---------- */

function safeValue(value){

 if(value === null || value === undefined){
   return "";
 }

 return String(value)
   .replace(/"/g,"&quot;");
}


/* ---------- INPUT EVENTS ---------- */

document.addEventListener("input",function(event){

 if(!event.target.matches(".measurement-row input")){
   return;
 }

 const id =
   Number(event.target.dataset.id);

 const field =
   event.target.dataset.field;

 updateRow(
   id,
   field,
   event.target.value
 );
});


/* ---------- KEYBOARD ---------- */

document.addEventListener("keydown",function(event){

 if(!event.target.matches(".measurement-row input")){
   return;
 }

 if(event.key === "Enter"){

   event.preventDefault();

   const current =
     event.target.closest(".measurement-row");

   const inputs =
     [...current.querySelectorAll("input")];

   const index =
     inputs.indexOf(event.target);

   /* જો qty field પર Enter */

   if(index === 2){

     const allRows =
       [...document.querySelectorAll(
         ".measurement-row"
       )];

     const rowIndex =
       allRows.indexOf(current);

     if(rowIndex === allRows.length-1){

       addRow(true);

     }else{

       allRows[rowIndex+1]
         .querySelector("input")
         .focus();
     }

     return;
   }

   /* Enter = next field */

   if(inputs[index+1]){
     inputs[index+1].focus();
   }
 }
});


/* ---------- RESULT ---------- */

function showResult(){

 const list =
   document.getElementById("resultList");

 const search =
   document.getElementById("search")
   .value.trim();

 list.innerHTML = "";

 let total = 0;

 let filtered =
   sortedRows();

 if(search !== ""){

   filtered =
     filtered.filter(row =>

       String(row.big).includes(search) ||
       String(row.small).includes(search)
     );
 }


 const validRows =
   filtered.filter(row =>

     row.big !== "" &&
     row.small !== "" &&
     row.qty !== ""
   );


 if(validRows.length === 0){

   list.innerHTML = `
     <div class="empty">
       કોઈ માપ નથી.<br>
       "＋ ઉમેરો" દબાવીને માપ નાખો.
     </div>
   `;

 }else{

   validRows.forEach(row=>{

     total += Number(row.qty) || 0;

     const div =
       document.createElement("div");

     div.className = "measure";

     div.innerHTML = `

       <span>
         ${row.big}×${row.small}=${row.qty}
       </span>

       <span class="qty">
         Qty: ${row.qty}
       </span>

     `;

     list.appendChild(div);
   });
 }

 document.getElementById("total").innerText =
   "કુલ જથ્થો: " + total;
}


/* ---------- CLEAR ---------- */

function clearAll(){

 const yes =
   confirm(
     "શું બધી measurements કાઢી નાખવી છે?"
   );

 if(!yes) return;

 rows = [];

 localStorage.removeItem(STORAGE_KEY);

 addRow(false);

 document.getElementById("search").value = "";

 saveData();

 render();
}


/* ---------- START ---------- */

loadData();

</script>

</body>
</html>

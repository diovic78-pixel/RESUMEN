<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <title>Panel de Trazabilidad de Descarga de Buques</title>
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <style>
    body { font-family: 'Segoe UI', Arial, sans-serif; background: #f4f6fa; margin: 0; color: #19324c;}
    header { background: #102842; color: white; padding: 1rem; }
    h1 { margin: 0.5rem; }
    .container { max-width: 1180px; margin: 2rem auto; padding: 1rem; background: white; border-radius: 14px; box-shadow: 0 2px 8px rgba(0,0,0,0.08);}
    section { margin-bottom: 2rem;}
    .modulo { padding: 1.2rem 1rem; margin-bottom: 2rem; border: 1px solid #dbe3eb; border-radius: 12px; background: #f9fbfd;}
    label { font-weight: 500; }
    input, select, button, textarea { margin-top: 0.2rem; margin-bottom: 0.6rem; padding: 0.4rem; border-radius: 7px; border: 1px solid #ccd2db;}
    button { background: #28568d; color: #fff; cursor: pointer; border: none; transition: background 0.2s;}
    button:hover { background: #163357;}
    table { width: 100%; border-collapse: collapse; margin-top: 1rem;}
    th, td { border: 1px solid #c7d3e6; padding: 0.6rem; text-align: left;}
    th { background: #e7f0fa;}
    .flex-row { display: flex; gap: 1.5rem; flex-wrap: wrap;}
    .flex-col-2 { flex: 1 1 310px;}
    .summary { margin-top: 1rem; background: #e1eaf6; padding: 1rem; border-radius: 8px;}
    .dashboard-card { background: #e6eef7; margin: 0.5rem; border-radius: 7px; padding: 1rem;}
    .small { font-size: 0.88em; }
    .status-badge { padding: 0.2em 0.7em; border-radius: 6px; color: #fff; font-size: 0.96em;}
    .cargado { background: #13ae60;}
    .no-cargado {background: #ff5858;}
    @media (max-width: 760px) {
      .container {padding: 0.3rem;}
      .flex-row {flex-direction: column;}
    }
  </style>
</head>
<body>
<header>
  <h1>Panel de Trazabilidad de Descarga de Buques</h1>
</header>
<div class="container">

  <!-- Información general -->
  <section class="modulo" id="mod-info-general">
    <h2>Información General del Buque</h2>
    <div class="flex-row">
      <div class="flex-col-2">
        <label>Nombre:</label>
        <input id="buque-nombre">
      </div>
      <div class="flex-col-2">
        <label>Matrícula:</label>
        <input id="buque-matricula">
      </div>
      <div class="flex-col-2">
        <label>Bandera:</label>
        <input id="buque-bandera">
      </div>
      <div class="flex-col-2">
        <label>Puerto Origen:</label>
        <input id="buque-origen">
      </div>
    </div>
    <div class="flex-row">
      <div class="flex-col-2">
        <label>Puerto Destino:</label>
        <input id="buque-destino">
      </div>
      <div class="flex-col-2">
        <label>ETA (Fecha estimada):</label>
        <input type="datetime-local" id="buque-eta">
      </div>
      <div class="flex-col-2">
        <label>Fecha de NOR:</label>
        <input type="datetime-local" id="buque-nor">
      </div>
      <div class="flex-col-2">
        <label>Rata Contratada TM/día:</label>
        <input type="number" id="buque-rata" min="1">
      </div>
    </div>
    <div class="flex-row">
      <div class="flex-col-2">
        <label>Condición Contrato:</label>
        <select id="buque-condicion">
          <option value="SHINC">SHINC</option>
          <option value="SHEX">SHEX</option>
        </select>
      </div>
      <div class="flex-col-2">
        <label>Cantidad total de producto (TM):</label>
        <input type="number" id="buque-tm" min="0">
      </div>
      <div class="flex-col-2">
        <label>Inicio de Operaciones:</label>
        <input type="datetime-local" id="inicio-op">
      </div>
      <div class="flex-col-2">
        <label>Fin de Operaciones:</label>
        <input type="datetime-local" id="fin-op">
      </div>
    </div>
    <div class="summary" id="info-general-resumen">
      <div><b>Laytime Disponible</b>: <span id="laytime"></span></div>
      <div><b>Tiempo en Operación</b>: <span id="tiempo-operacion"></span></div>
    </div>
  </section>

  <!-- Productos manifestados/descargados -->
  <section class="modulo">
    <h2>Productos Manifestados</h2>
    <table id="tabla-productos">
      <thead>
        <tr>
          <th>Tipo Producto</th><th>Bodega</th><th>Declarado (TM)</th><th>Descargado (TM)</th><th>Acción</th>
        </tr>
      </thead>
      <tbody id="tbody-productos"></tbody>
    </table>
    <button onclick="agregarProducto()">Agregar Producto</button>
  </section>

  <!-- Registro de Paradas -->
  <section class="modulo">
    <h2>Registro de Paradas / Incidentes</h2>
    <form onsubmit="registrarParada(event)">
      <div class="flex-row">
        <div class="flex-col-2">
          <label>Motivo:</label>
          <input required id="parada-motivo">
        </div>
        <div class="flex-col-2">
          <label>Inicio (fecha-hora):</label>
          <input required type="datetime-local" id="parada-inicio">
        </div>
        <div class="flex-col-2">
          <label>Fin (fecha-hora):</label>
          <input required type="datetime-local" id="parada-fin">
        </div>
        <div class="flex-col-2" style="align-self: flex-end;">
          <button type="submit">Registrar</button>
        </div>
      </div>
    </form>
    <table id="tabla-paradas">
      <thead>
        <tr>
          <th>Motivo</th><th>Inicio</th><th>Fin</th><th>Duración (h:m)</th><th>Acción</th>
        </tr>
      </thead>
      <tbody id="tbody-paradas"></tbody>
    </table>
    <div class="summary"><b>Tiempo Total Paradas:</b> <span id="tiempo-paradas"></span></div>
  </section>

  <!-- Tracking de Camiones -->
  <section class="modulo">
    <h2>Tracking de Camiones</h2>
    <form onsubmit="registrarCamion(event)">
      <div class="flex-row">
        <div class="flex-col-2">
          <label>Placa:</label>
          <input required id="camion-placa">
        </div>
        <div class="flex-col-2">
          <label>Ficha:</label>
          <input required id="camion-ficha">
        </div>
        <div class="flex-col-2">
          <label>Estatus:</label>
          <select id="camion-estatus">
            <option value="Cargado">Cargado</option>
            <option value="No Cargado">No Cargado</option>
          </select>
        </div>
        <div class="flex-col-2" style="align-self: flex-end;">
          <button type="submit">Registrar</button>
        </div>
      </div>
    </form>
    <div style="margin-bottom:0.7em;">
      <label>Filtrar por Estatus:</label>
      <select onchange="filtrarCamiones()" id="filtro-estatus">
        <option value="Todos">Todos</option>
        <option value="Cargado">Cargado</option>
        <option value="No Cargado">No Cargado</option>
      </select>
    </div>
    <table id="tabla-camiones">
      <thead>
        <tr>
          <th>Placa</th><th>Ficha</th><th>Estatus</th><th>Acción</th>
        </tr>
      </thead>
      <tbody id="tbody-camiones"></tbody>
    </table>
    <div class="summary">
      <b>Camiones registrados:</b> <span id="camion-total"></span> |
      <b>Camiones por hora (simulado):</b> <span id="camion-xhora"></span>
    </div>
  </section>

  <!-- Correa mecanizada -->
  <section class="modulo">
    <h2>Correa Mecanizada (por hora)</h2>
    <form onsubmit="registrarCorrea(event)">
      <div class="flex-row">
        <div class="flex-col-2">
          <label>Línea:</label>
          <select id="correa-linea">
            <option value="1">Línea 1</option>
            <option value="2">Línea 2</option>
          </select>
        </div>
        <div class="flex-col-2">
          <label>Producto:</label>
          <input required id="correa-producto">
        </div>
        <div class="flex-col-2">
          <label>Toneladas:</label>
          <input required type="number" id="correa-tn" min="1">
        </div>
        <div class="flex-col-2">
          <label>Hora:</label>
          <input required type="datetime-local" id="correa-hora">
        </div>
        <div class="flex-col-2" style="align-self: flex-end;">
          <button type="submit">Registrar</button>
        </div>
      </div>
    </form>
    <table id="tabla-correa">
      <thead>
        <tr>
          <th>Línea</th><th>Producto</th><th>Toneladas</th><th>Hora</th><th>Acción</th>
        </tr>
      </thead>
      <tbody id="tbody-correa"></tbody>
    </table>
    <div class="summary">
      <b>Total Descargado por Correa:</b> <span id="total-correa"></span> TM
    </div>
  </section>

  <!-- Total descargado -->
  <section class="modulo">
    <h2>Total Descargado</h2>
    <div class="summary" id="total-descargado"></div>
  </section>

  <!-- Dashboard y resumen ejecutivo -->
  <section class="modulo">
    <h2>Dashboard y Resumen Ejecutivo</h2>
    <div class="dashboard-card"><span><b>Tiempo Bruto de Operación:</b> <span id="dsh-tiempo-bruto"></span></span></div>
    <div class="dashboard-card"><span><b>Tiempo Neto de Operación:</b> <span id="dsh-tiempo-neto"></span></span></div>
    <div class="dashboard-card"><span><b>TPH Promedio (bruto/neto):</b> <span id="dsh-tph"></span></span></div>
    <div class="dashboard-card"><span><b>Tiempo Hábil Restante:</b> <span id="dsh-laytime-rest"></span></span></div>
    <div class="dashboard-card">
      <label>Gráfica de descarga por hora (simulada):</label>
      <canvas id="grafico-horas" height="120"></canvas>
    </div>
  </section>

</div>

<script>
// ----- Estado de la plantilla -----
let productos = [], paradas=[], camiones=[], correa=[];
let laytimeHoras = 0;

// ----- General buque: Laytime y tiempo operación -----
function calcLaytime() {
  const tm=parseFloat(document.getElementById('buque-tm').value);
  const rata=parseFloat(document.getElementById('buque-rata').value);
  const condicion=document.getElementById('buque-condicion').value;
  if (tm>0 && rata>0) {
    let dias = tm/rata;
    if (condicion==='SHEX') dias *= (7/5); // Simula SHEX como días laborales
    laytimeHoras = Math.round(dias*24);
    document.getElementById('laytime').innerText = laytimeHoras + " horas";
  } else {
    document.getElementById('laytime').innerText = "No definido";
  }
}
['buque-tm','buque-rata','buque-condicion'].forEach(id=>{
  document.getElementById(id).addEventListener('input',calcLaytime);
});

// Calcula tiempo operación en vivo si hay inicio, fin o solo inicio
function calcOperacion() {
  const ini=document.getElementById('inicio-op').value;
  const fin=document.getElementById('fin-op').value;
  if (!ini) {document.getElementById('tiempo-operacion').innerText="No iniciado";return;}
  const dtIni = new Date(ini);
  let dtFin = fin? new Date(fin): new Date();
  let diff = Math.max((dtFin-dtIni)/3600000,0); // en horas
  document.getElementById('tiempo-operacion').innerText = (diff).toFixed(2) + " h";
  return diff;
}
['inicio-op','fin-op'].forEach(id=>{
  document.getElementById(id).addEventListener('input',()=>{ calcOperacion();updateDashboard(); });
});

// ----- Productos -----
function renderProductos() {
  const tbody=document.getElementById('tbody-productos'); tbody.innerHTML="";
  productos.forEach((p,i)=>{
    tbody.innerHTML+=`<tr>
      <td>${p.tipo}</td>
      <td>${p.bodega}</td>
      <td><input type='number' min='0' style='width:70px' value='${p.declarado}' onchange='actProdDeclarado(${i}, this.value)'></td>
      <td><input type='number' min='0' style='width:70px' value='${p.descargado}' onchange='actProdDescargado(${i}, this.value)'></td>
      <td><button onclick="eliminaProducto(${i})">Eliminar</button></td>
    </tr>`;
  });
}
function actProdDeclarado(idx,vl){ productos[idx].declarado=Number(vl); renderProductos(); updateDashboard();}
function actProdDescargado(idx,vl){ productos[idx].descargado=Number(vl); renderProductos(); updateDashboard();}
function agregarProducto() {
  productos.push({tipo:"",bodega:"",declarado:0,descargado:0});
  renderProductos();
}
function eliminaProducto(idx){
  productos.splice(idx,1);
  renderProductos();
  updateDashboard();
}
document.addEventListener('DOMContentLoaded',()=>{ renderProductos(); });

// ----- Registro de paradas/incidentes -----
function registrarParada(e) {
  e.preventDefault();
  let motivo=document.getElementById('parada-motivo').value,
      ini=document.getElementById('parada-inicio').value,
      fin=document.getElementById('parada-fin').value;
  paradas.push({motivo,ini,fin});
  renderParadas();
  updateDashboard();
}
function renderParadas() {
  const tbody=document.getElementById('tbody-paradas'); tbody.innerHTML="";
  paradas.forEach((p,i)=>{
    let duration="", diff=0;
    if (p.ini && p.fin) {
      const d1=new Date(p.ini), d2=new Date(p.fin);
      diff=(d2-d1)/3600000;
      duration=`${Math.floor(diff)}h ${Math.round((diff%1)*60)}m`;
    }
    tbody.innerHTML+=`<tr>
      <td>${p.motivo}</td>
      <td>${p.ini.replace("T", " ")}</td>
      <td>${p.fin.replace("T", " ")}</td>
      <td>${duration}</td>
      <td><button onclick="eliminaParada(${i})">Eliminar</button></td>
    </tr>`;
  });
  // Tiempo total
  let total=0;
  paradas.forEach(p=>{
    if (p.ini&&p.fin) total+=((new Date(p.fin))-(new Date(p.ini)))/3600000;
  });
  document.getElementById('tiempo-paradas').innerText=total.toFixed(2)+' h';
}
function eliminaParada(idx){paradas.splice(idx,1);renderParadas();updateDashboard();}

// ----- Tracking de camiones -----
function registrarCamion(e){
  e.preventDefault();
  let placa=document.getElementById('camion-placa').value,
      ficha=document.getElementById('camion-ficha').value,
      estatus=document.getElementById('camion-estatus').value;
  camiones.push({placa,ficha,estatus});
  renderCamiones();
}
function renderCamiones(){
  const tbody=document.getElementById('tbody-camiones'); tbody.innerHTML="";
  let filtro=document.getElementById('filtro-estatus').value;
  let visibles=camiones.filter(c=>
    filtro==="Todos"? true : c.estatus===filtro);
  visibles.forEach((c,i)=>{
    tbody.innerHTML+=`<tr>
      <td>${c.placa}</td>
      <td>${c.ficha}</td>
      <td>
        <span class="status-badge ${c.estatus==='Cargado'?'cargado':'no-cargado'}">${c.estatus}</span>
      </td>
      <td><button onclick="eliminaCamion(${i})">Eliminar</button></td>
    </tr>`;
  });
  document.getElementById('camion-total').innerText=camiones.length;
  // Simulemos camiones/hora (ficticio)
  document.getElementById('camion-xhora').innerText = Math.round(camiones.length / (Math.max(calcOperacion()||1,1)/24)).toFixed(1);
}
function eliminaCamion(idx){ camiones.splice(idx,1);renderCamiones();}
function filtrarCamiones(){renderCamiones();}

// ----- Correa mecanizada -----
function registrarCorrea(e){
  e.preventDefault();
  let linea=document.getElementById('correa-linea').value,
      prod=document.getElementById('correa-producto').value,
      tn=document.getElementById('correa-tn').value,
      hora=document.getElementById('correa-hora').value;
  correa.push({linea,prod,tn:Number(tn),hora});
  renderCorrea();
  updateDashboard();
}
function renderCorrea(){
  const tbody=document.getElementById('tbody-correa'); tbody.innerHTML="";
  correa.forEach((c,i)=>{
    tbody.innerHTML+=`<tr>
      <td>${c.linea}</td><td>${c.prod}</td><td>${c.tn}</td><td>${c.hora.replace("T"," ")}</td>
      <td><button onclick="eliminaCorrea(${i})">Eliminar</button></td>
    </tr>`;
  });
  // Total descargado
  let total = correa.reduce((acc,c)=>acc+c.tn,0);
  document.getElementById('total-correa').innerText=total.toFixed(2);
}
function eliminaCorrea(idx){ correa.splice(idx,1);renderCorrea();updateDashboard();}

// ----- Total descargado -----
function updateTotales() {
  let totalCamion = productos.reduce((a,p)=>a+p.descargado,0);
  let totalCorrea = correa.reduce((a,c)=>a+c.tn,0);
  document.getElementById('total-descargado').innerHTML = 
    `<b>Total descargado camión:</b> ${totalCamion.toFixed(2)} TM <b>| Correa:</b> ${totalCorrea.toFixed(2)} TM <b>| Total:</b> ${(totalCamion+totalCorrea).toFixed(2)} TM`;
}

// ----- Dashboard/Resúmenes -----
function updateDashboard(){
  // Tiempo bruto y neto
  let bruto=calcOperacion()||0;
  let tiempoParadas=0; paradas.forEach(p=> {
    if (p.ini&&p.fin) tiempoParadas+=((new Date(p.fin))-(new Date(p.ini)))/3600000;
  });
  let neto = Math.max(bruto-tiempoParadas,0);
  document.getElementById('dsh-tiempo-bruto').innerText=bruto.toFixed(2) + " h";
  document.getElementById('dsh-tiempo-neto').innerText=neto.toFixed(2) + " h";
  // TPH
  let tot = productos.reduce((a,p)=>a+p.descargado,0) + correa.reduce((a,c)=>a+c.tn,0);
  let tphBruto = bruto > 0 ? (tot/bruto) : 0;
  let tphNeto  = neto  > 0 ? (tot/neto)  : 0;
  document.getElementById('dsh-tph').innerText=`${tphBruto.toFixed(1)} / ${tphNeto.toFixed(1)} TM/h`;
  // Laytime restante
  let disponible = laytimeHoras-(bruto||0);
  document.getElementById('dsh-laytime-rest').innerText = (disponible>0?disponible.toFixed(2):'0')+' horas';
  // Update totales
  updateTotales();
  // Update gráfica
  updateGrafico();
}
setInterval(updateDashboard,30000); // Actualizado cada 30 segs
document.addEventListener('DOMContentLoaded',()=>{ updateDashboard(); renderParadas(); renderCamiones(); renderCorrea(); });

// ----- Gráfica descarga por hora -----
function updateGrafico(){
  let ctx=document.getElementById('grafico-horas').getContext('2d');
  ctx.clearRect(0,0,600,120);
  // Simular datos cada hora...
  let horas = {};
  correa.forEach(c=>{
    let h=(c.hora||"").split("T")[1]? (c.hora.split("T")[1].substr(0,5)):"";
    if (!horas[h]) horas[h]=0;
    horas[h]+=c.tn;
  });
  let labels=Object.keys(horas); let vals=labels.map(l=>horas[l]);
  // Escalado
  let maxV = Math.max(...vals,1);
  let W=ctx.canvas.width, H=ctx.canvas.height;
  let step = labels.length>1? W/(labels.length-1): W;
  ctx.beginPath();
  ctx.strokeStyle="#2355a4"; ctx.lineWidth=2;
  labels.forEach((h,i)=>{
    let x = i*step, y = H-(vals[i]/maxV*0.8*H)-18;
    if(i==0) ctx.moveTo(x,y);
    else ctx.lineTo(x,y);
    ctx.arc(x, y, 2.3, 0, 2*Math.PI);
  }); ctx.stroke();
  ctx.fillStyle="#333";
  labels.forEach((h,i)=> ctx.fillText(h, i*step, H-2));
  ctx.fillStyle="#237acf";
  labels.forEach((h,i)=> ctx.fillRect(i*step-2, H-(vals[i]/maxV*0.8*H)-8,4,8));
}
</script>
</body>
</html>

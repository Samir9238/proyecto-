<template>
  <div class="min-h-screen bg-slate-200/70 text-slate-900 font-sans p-4 sm:p-8 flex flex-col items-center">
    <!-- Tarjeta principal con fondo mate neutro sin encandilar -->
    <div class="w-full max-w-[98%] bg-slate-100 rounded-2xl border border-slate-300 shadow-xl p-6 relative overflow-hidden">
      
      <!-- Detalle sutil verde institucional -->
      <div class="absolute -top-24 -left-24 w-80 h-80 bg-emerald-600/10 rounded-full blur-3xl pointer-events-none"></div>

      <!-- Header de alto contraste visual -->
      <div class="relative z-10 flex flex-col md:flex-row md:items-center justify-between pb-6 border-b border-slate-300/80 gap-4">
        <div>
          <div class="flex items-center gap-2 mb-1">
            <span class="w-2.5 h-2.5 rounded-full bg-emerald-600 shadow-[0_0_8px_#16a34a]"></span>
            <span class="text-[11px] font-black uppercase tracking-widest text-emerald-800">Plataforma Institucional</span>
          </div>
          <h1 class="text-2xl font-black tracking-tight text-slate-900">
            Registro Masivo <span class="text-emerald-700">Excel</span>
          </h1>
          <p class="text-xs text-slate-600 font-medium mt-1">
            Copia tus celdas desde Excel y presiona <kbd class="px-2 py-0.5 bg-slate-200 border border-slate-300 rounded text-[10px] font-mono font-bold text-slate-800">Ctrl + V</kbd> dentro de la tabla.
          </p>
        </div>

        <!-- Contadores y Botones con mejor lectura -->
        <div class="flex items-center gap-3">
          <div class="px-3.5 py-2 rounded-xl bg-slate-200/80 border border-slate-300 text-xs text-slate-800 font-bold">
            Registros: <span class="font-black text-emerald-800 text-sm ml-1">{{ rows.length }}</span>
          </div>

          <button 
            @click="addRow" 
            class="px-4 py-2 bg-emerald-700 hover:bg-emerald-800 text-white font-bold text-xs rounded-xl shadow-md transition active:scale-95"
          >
            + Nueva Fila
          </button>

          <button 
            @click="clearAll" 
            class="px-4 py-2 bg-slate-200 hover:bg-rose-100 text-slate-700 hover:text-rose-700 border border-slate-300 rounded-xl text-xs font-bold transition"
          >
            Limpiar
          </button>
        </div>
      </div>

      <!-- Tabla con encabezado verde mate profundo y celdas legibles -->
      <div class="relative z-10 mt-6 overflow-x-auto rounded-xl border border-slate-300 bg-slate-50 shadow-sm" @paste="handlePaste">
        <table class="w-full border-collapse text-xs text-left">
          <thead>
            <tr class="bg-emerald-800 text-white font-extrabold uppercase text-[10px] tracking-wider border-b-2 border-emerald-900 select-none">
              <th class="py-3.5 px-3 border-r border-emerald-700/80 w-10 text-center font-mono">#</th>
              <th class="py-3.5 px-3 border-r border-emerald-700/80 min-w-[150px]">N° Identificación</th>
              <th class="py-3.5 px-3 border-r border-emerald-700/80 min-w-[130px]">Primer Nombre</th>
              <th class="py-3.5 px-3 border-r border-emerald-700/80 min-w-[130px]">Segundo Nombre</th>
              <th class="py-3.5 px-3 border-r border-emerald-700/80 min-w-[200px]">Correo Personal</th>
              <th class="py-3.5 px-3 border-r border-emerald-700/80 min-w-[120px]">Teléfono</th>
              <th class="py-3.5 px-3 border-r border-emerald-700/80 min-w-[130px]">Cargo</th>
              <th class="py-3.5 px-3 border-r border-emerald-700/80 min-w-[150px]">Tipo Vinculación</th>
              <th class="py-3.5 px-3 border-r border-emerald-700/80 min-w-[140px]">Departamento</th>
              <th class="py-3.5 px-3 border-r border-emerald-700/80 min-w-[130px]">Centro de Costo</th>
              <th class="py-3.5 px-3 border-r border-emerald-700/80 min-w-[120px]">Sede</th>
              <th class="py-3.5 px-2 w-8 text-center"></th>
            </tr>
          </thead>
          <tbody class="divide-y divide-slate-300">
            <tr 
              v-for="(row, index) in rows" 
              :key="index" 
              class="hover:bg-emerald-100/50 transition group"
            >
              <!-- Index -->
              <td class="py-2.5 px-3 border-r border-slate-300 text-center font-mono text-emerald-900 bg-slate-200/60 font-bold">
                {{ String(index + 1).padStart(2, '0') }}
              </td>

              <!-- 1. Identificación -->
              <td class="p-0 border-r border-slate-300">
                <input 
                  v-model="row.identificacion" 
                  type="text" 
                  placeholder="Sin datos..."
                  class="w-full px-3 py-2 bg-transparent text-slate-900 font-mono text-[11px] font-semibold placeholder-slate-400 outline-none focus:bg-white focus:ring-2 focus:ring-emerald-600 transition"
                />
              </td>

              <!-- 2. Primer Nombre -->
              <td class="p-0 border-r border-slate-300">
                <input 
                  v-model="row.primerNombre" 
                  type="text" 
                  placeholder="Primer nombre"
                  class="w-full px-3 py-2 bg-transparent text-slate-900 font-medium placeholder-slate-400 outline-none focus:bg-white focus:ring-2 focus:ring-emerald-600 transition"
                />
              </td>

              <!-- 3. Segundo Nombre -->
              <td class="p-0 border-r border-slate-300">
                <input 
                  v-model="row.segundoNombre" 
                  type="text" 
                  placeholder="Segundo nombre"
                  class="w-full px-3 py-2 bg-transparent text-slate-800 placeholder-slate-400 outline-none focus:bg-white focus:ring-2 focus:ring-emerald-600 transition"
                />
              </td>

              <!-- 4. Correo Personal -->
              <td class="p-0 border-r border-slate-300">
                <input 
                  v-model="row.correoPersonal" 
                  type="text" 
                  placeholder="correo@ejemplo.com"
                  class="w-full px-3 py-2 bg-transparent text-emerald-900 font-mono text-[11px] font-semibold placeholder-slate-400 outline-none focus:bg-white focus:ring-2 focus:ring-emerald-600 transition"
                />
              </td>

              <!-- 5. Teléfono -->
              <td class="p-0 border-r border-slate-300">
                <input 
                  v-model="row.telefono" 
                  type="text" 
                  placeholder="3000000000"
                  class="w-full px-3 py-2 bg-transparent text-slate-900 font-mono text-[11px] font-semibold placeholder-slate-400 outline-none focus:bg-white focus:ring-2 focus:ring-emerald-600 transition"
                />
              </td>

              <!-- 6. Cargo -->
              <td class="p-0 border-r border-slate-300">
                <input 
                  v-model="row.cargo" 
                  type="text" 
                  placeholder="Cargo..."
                  class="w-full px-3 py-2 bg-transparent text-slate-800 placeholder-slate-400 outline-none focus:bg-white focus:ring-2 focus:ring-emerald-600 transition"
                />
              </td>

              <!-- 7. Tipo de Vinculación -->
              <td class="p-0 border-r border-slate-300">
                <input 
                  v-model="row.tipoVinculacion" 
                  type="text" 
                  placeholder="Vinculación..."
                  class="w-full px-3 py-2 bg-transparent text-emerald-900 font-bold placeholder-slate-400 outline-none focus:bg-white focus:ring-2 focus:ring-emerald-600 transition"
                />
              </td>

              <!-- 8. Departamento -->
              <td class="p-0 border-r border-slate-300">
                <input 
                  v-model="row.departamento" 
                  type="text" 
                  placeholder="Departamento..."
                  class="w-full px-3 py-2 bg-transparent text-slate-800 placeholder-slate-400 outline-none focus:bg-white focus:ring-2 focus:ring-emerald-600 transition"
                />
              </td>

              <!-- 9. Centro de Costo -->
              <td class="p-0 border-r border-slate-300">
                <input 
                  v-model="row.centroCosto" 
                  type="text" 
                  placeholder="Centro de costo..."
                  class="w-full px-3 py-2 bg-transparent text-slate-800 placeholder-slate-400 outline-none focus:bg-white focus:ring-2 focus:ring-emerald-600 transition"
                />
              </td>

              <!-- 10. Sede -->
              <td class="p-0 border-r border-slate-300">
                <input 
                  v-model="row.sede" 
                  type="text" 
                  placeholder="Sede..."
                  class="w-full px-3 py-2 bg-transparent text-slate-800 placeholder-slate-400 outline-none focus:bg-white focus:ring-2 focus:ring-emerald-600 transition"
                />
              </td>

              <!-- Eliminar Fila -->
              <td class="p-1 text-center">
                <button 
                  @click="removeRow(index)" 
                  class="text-slate-400 hover:text-rose-600 font-bold p-1 rounded opacity-0 group-hover:opacity-100 transition"
                >
                  ✕
                </button>
              </td>
            </tr>
          </tbody>
        </table>
      </div>

      <!-- Barra Inferior -->
      <div class="relative z-10 mt-6 flex flex-col sm:flex-row items-center justify-between gap-4">
        <p class="text-xs text-slate-600 font-semibold">
          ⚡ Sincronización oficial de 10 columnas en formato JSON.
        </p>

        <button 
          @click="submitAll" 
          :disabled="loading || rows.length === 0"
          class="w-full sm:w-auto px-8 py-3 bg-emerald-700 hover:bg-emerald-800 text-white font-black text-xs uppercase tracking-wider rounded-xl shadow-lg transition active:scale-95 disabled:opacity-40"
        >
          <span v-if="loading">Procesando {{ rows.length }} usuarios...</span>
          <span v-else>Guardar {{ rows.length }} Usuario(s)</span>
        </button>
      </div>

      <!-- Alerta de Éxito clara y suave -->
      <div v-if="successMessage" class="relative z-10 mt-4 p-4 bg-emerald-100 border border-emerald-300 text-emerald-900 text-xs rounded-xl font-bold">
        ✨ {{ successMessage }}
      </div>

    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const emptyRow = () => ({
  identificacion: '',
  primerNombre: '',
  segundoNombre: '',
  correoPersonal: '',
  telefono: '',
  cargo: '',
  tipoVinculacion: '',
  departamento: '',
  centroCosto: '',
  sede: ''
})

const rows = ref([emptyRow()])

const loading = ref(false)
const successMessage = ref('')

const handlePaste = (event) => {
  event.preventDefault()
  
  const clipboardData = event.clipboardData || window.clipboardData
  const pastedText = clipboardData.getData('text')

  if (!pastedText) return

  const lines = pastedText.trim().split(/\r\n|\n|\r/)
  
  const parsedRows = lines.map(line => {
    const cols = line.split('\t')
    return {
      identificacion: cols[0]?.trim() || '',
      primerNombre: cols[1]?.trim() || '',
      segundoNombre: cols[2]?.trim() || '',
      correoPersonal: cols[3]?.trim() || '',
      telefono: cols[4]?.trim() || '',
      cargo: cols[5]?.trim() || '',
      tipoVinculacion: cols[6]?.trim() || '',
      departamento: cols[7]?.trim() || '',
      centroCosto: cols[8]?.trim() || '',
      sede: cols[9]?.trim() || ''
    }
  })

  if (rows.value.length === 1 && !rows.value[0].identificacion && !rows.value[0].primerNombre) {
    rows.value = parsedRows
  } else {
    rows.value = [...rows.value, ...parsedRows]
  }
}

const addRow = () => {
  rows.value.push(emptyRow())
}

const removeRow = (index) => {
  rows.value.splice(index, 1)
}

const clearAll = () => {
  rows.value = [emptyRow()]
  successMessage.value = ''
}

const submitAll = () => {
  loading.value = true
  successMessage.value = ''

  const datosParaBackend = rows.value.map(row => ({
    employeeId: row.identificacion,
    firstName: row.primerNombre,
    lastName: row.segundoNombre,
    recoveryEmail: row.correoPersonal,
    mobilePhone: row.telefono,
    employeeTitle: row.cargo,
    employeeType: row.tipoVinculacion,
    department: row.departamento,
    costCenter: row.centroCosto,
    floorSection: row.sede
  }))

  const jsonFinal = JSON.stringify(datosParaBackend, null, 2)

  console.log('🚀 ¡JSON PARA EL BACKEND!')
  console.log(jsonFinal)

  setTimeout(() => {
    loading.value = false
    successMessage.value = `¡Listos! Se guardaron exitosamente los ${rows.value.length} registros.`
  }, 800)
}
</script>
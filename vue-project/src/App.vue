<template>
  <div class="flex h-screen bg-slate-200/70 text-slate-900 font-sans overflow-hidden relative">
    
    <!-- ==================== MODAL DE CARGA FLOTANTE (OVERLAY) ==================== -->
    <div 
      v-if="isPasting" 
      class="fixed inset-0 z-50 bg-slate-950/70 backdrop-blur-md flex items-center justify-center p-4 transition-all duration-300"
    >
      <div class="bg-slate-900 border border-slate-700 rounded-3xl p-8 max-w-md w-full shadow-2xl text-center space-y-6">
        
        <div class="relative w-20 h-20 mx-auto flex items-center justify-center">
          <div 
            class="absolute inset-0 rounded-full border-4 border-t-transparent animate-spin"
            :class="[
              activeTab === 'add' ? 'border-emerald-500' : 
              activeTab === 'delete' ? 'border-rose-500' : 'border-amber-500'
            ]"
          ></div>
          <span class="text-2xl">
            {{ activeTab === 'add' ? '📥' : activeTab === 'delete' ? '🗑️' : '🚫' }}
          </span>
        </div>

        <div>
          <h3 class="text-lg font-black text-white tracking-wide">
            Procesando Importación Masiva
          </h3>
          <p class="text-xs text-slate-400 mt-1 font-medium">
            Procesando en lotes de 500 registros para máxima fluidez.
          </p>
        </div>

        <div class="space-y-2">
          <div class="flex justify-between items-center text-xs font-bold font-mono">
            <span class="text-slate-400">Progreso:</span>
            <span 
              :class="[
                activeTab === 'add' ? 'text-emerald-400' : 
                activeTab === 'delete' ? 'text-rose-400' : 'text-amber-400'
              ]"
            >
              {{ pastePercent }}%
            </span>
          </div>

          <div class="w-full h-3 bg-slate-800 rounded-full overflow-hidden border border-slate-700 p-0.5">
            <div 
              class="h-full rounded-full transition-all duration-150"
              :class="[
                activeTab === 'add' ? 'bg-emerald-500' : 
                activeTab === 'delete' ? 'bg-rose-500' : 'bg-amber-500'
              ]"
              :style="{ width: pastePercent + '%' }"
            ></div>
          </div>
        </div>

        <div class="bg-slate-950/60 rounded-xl p-3 border border-slate-800 font-mono text-xs text-slate-300">
          Cargados: <strong class="text-white">{{ pasteProcessedCount.toLocaleString() }}</strong> / <span>{{ pasteTotalCount.toLocaleString() }}</span> registros
        </div>

      </div>
    </div>

    <!-- ==================== SIDEBAR IZQUIERDO ==================== -->
    <aside class="w-64 bg-slate-900 text-slate-300 flex flex-col justify-between flex-shrink-0 shadow-2xl border-r border-slate-800">
      <div>
        <div class="h-16 flex items-center gap-3 px-6 bg-slate-950 border-b border-slate-800">
          <div class="w-9 h-9 rounded-xl bg-emerald-700 text-white font-black flex items-center justify-center text-sm shadow-md shadow-emerald-700/30 border border-emerald-500">
            UC
          </div>
          <div>
            <h2 class="text-xs font-black text-white uppercase tracking-wider">Unicórdoba</h2>
            <p class="text-[10px] text-emerald-400 font-medium">Portal de Gestión</p>
          </div>
        </div>

        <div class="p-4">
          <p class="text-[10px] font-black uppercase tracking-widest text-slate-500 px-3 mb-3">
            Módulos de Usuario
          </p>

          <nav class="space-y-1">
            <button 
              @click="switchTab('add')"
              :class="[
                'w-full flex items-center gap-3 px-3.5 py-2.5 rounded-xl font-bold text-xs transition-all duration-200',
                activeTab === 'add' 
                  ? 'bg-emerald-700 text-white shadow-md shadow-emerald-700/20' 
                  : 'text-slate-400 hover:bg-slate-800 hover:text-slate-200'
              ]"
            >
              <span class="text-sm">➕</span>
              <span>Agregar Usuario</span>
            </button>

            <button 
              @click="switchTab('delete')"
              :class="[
                'w-full flex items-center gap-3 px-3.5 py-2.5 rounded-xl font-bold text-xs transition-all duration-200',
                activeTab === 'delete' 
                  ? 'bg-rose-700 text-white shadow-md shadow-rose-700/20' 
                  : 'text-slate-400 hover:bg-slate-800 hover:text-slate-200'
              ]"
            >
              <span class="text-sm">🗑️</span>
              <span>Eliminar Usuario</span>
            </button>

            <button 
              @click="switchTab('suspend')"
              :class="[
                'w-full flex items-center gap-3 px-3.5 py-2.5 rounded-xl font-bold text-xs transition-all duration-200',
                activeTab === 'suspend' 
                  ? 'bg-amber-700 text-white shadow-md shadow-amber-700/20' 
                  : 'text-slate-400 hover:bg-slate-800 hover:text-slate-200'
              ]"
            >
              <span class="text-sm">🚫</span>
              <span>Suspender Usuario</span>
            </button>
          </nav>
        </div>
      </div>

      <div class="p-4 border-t border-slate-800/80 bg-slate-950/50">
        <div class="flex items-center gap-3">
          <div class="w-8 h-8 rounded-full bg-emerald-800 text-emerald-200 font-extrabold text-xs flex items-center justify-center">
            AD
          </div>
          <div class="overflow-hidden">
            <p class="text-xs font-bold text-white truncate">Admin Sistema</p>
            <p class="text-[10px] text-slate-500 truncate">admin@unicordoba.edu.co</p>
          </div>
        </div>
      </div>
    </aside>

    <!-- ==================== ÁREA PRINCIPAL DERECHA ==================== -->
    <div class="flex-1 flex flex-col min-w-0 overflow-hidden">
      
      <header class="h-16 bg-slate-100 border-b border-slate-300 flex items-center justify-between px-6 flex-shrink-0">
        <div class="flex items-center gap-3">
          <span class="w-2.5 h-2.5 rounded-full" :class="activeTab === 'add' ? 'bg-emerald-600' : activeTab === 'delete' ? 'bg-rose-600' : 'bg-amber-600'"></span>
          <span class="text-xs font-black uppercase tracking-wider text-slate-700">
            {{ currentTabTitle }}
          </span>
        </div>

        <div class="flex items-center gap-4">
          <span class="text-xs font-bold bg-slate-200 px-3 py-1 rounded-lg text-slate-700">
            Sesión Activa
          </span>
        </div>
      </header>

      <main class="flex-1 overflow-y-auto p-6 space-y-6">
        
        <!-- Tarjetas Resumen + BUSCADOR GENERAL -->
        <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
          <div class="bg-slate-100 border border-slate-300 rounded-2xl p-4 shadow-sm flex items-center justify-between">
            <div>
              <p class="text-[11px] font-bold uppercase text-slate-500">Registros en Tabla</p>
              <p class="text-2xl font-black text-slate-900 mt-0.5 font-mono">{{ currentRows.length.toLocaleString() }}</p>
            </div>
            <div class="w-10 h-10 rounded-xl bg-slate-200 text-slate-800 font-bold flex items-center justify-center text-lg">
              📊
            </div>
          </div>

          <div class="bg-slate-100 border border-slate-300 rounded-2xl p-4 shadow-sm flex flex-col justify-center">
            <label class="text-[11px] font-bold uppercase text-slate-500 mb-1 flex items-center justify-between">
              <span>🔍 Buscar en Registros</span>
              <span v-if="searchQuery" class="text-[10px] text-emerald-700 font-semibold font-mono">
                Filtrados: {{ filteredRows.length.toLocaleString() }}
              </span>
            </label>
            <div class="relative">
              <input 
                v-model="searchQuery" 
                type="text" 
                placeholder="Buscar por correo, identificación, nombre..." 
                class="w-full bg-white border border-slate-300 rounded-xl px-3 py-1.5 text-xs text-slate-800 focus:outline-none focus:ring-2 focus:ring-emerald-600 font-medium shadow-inner"
              />
              <button 
                v-if="searchQuery" 
                @click="searchQuery = ''" 
                class="absolute right-2.5 top-1.5 text-slate-400 hover:text-slate-600 text-xs font-bold"
              >
                ✕
              </button>
            </div>
          </div>
        </div>

        <!-- ==================== VISTA 1: AGREGAR USUARIO ==================== -->
        <section v-if="activeTab === 'add'" class="bg-slate-100 border border-slate-300 rounded-2xl shadow-md p-6">
          <div class="flex flex-col md:flex-row md:items-center justify-between pb-4 border-b border-slate-300 gap-4 mb-6">
            <div>
              <h2 class="text-lg font-black text-slate-900">Registro Masivo mediante Excel</h2>
              <p class="text-xs text-slate-600 mt-0.5">
                Pega tus celdas desde Excel presionando <kbd class="px-2 py-0.5 bg-slate-200 border border-slate-300 rounded text-[10px] font-mono font-bold text-slate-800">Ctrl + V</kbd> directamente sobre la tabla.
              </p>
            </div>

            <div class="flex items-center gap-2">
              <button @click="addRow('add')" class="px-4 py-2 bg-emerald-700 hover:bg-emerald-800 text-white font-bold text-xs rounded-xl shadow-md transition active:scale-95">
                + Nueva Fila
              </button>
              <button @click="clearAll('add')" class="px-4 py-2 bg-slate-200 hover:bg-rose-100 text-slate-700 hover:text-rose-700 border border-slate-300 rounded-xl text-xs font-bold transition">
                Limpiar
              </button>
            </div>
          </div>

          <div 
            class="overflow-x-auto overflow-y-auto rounded-xl border border-slate-300 bg-slate-50 shadow-xs h-[500px] relative"
            @scroll="onScroll"
            @paste="(e) => handlePaste(e, 'add')"
          >
            <table class="w-full border-collapse text-xs text-left" style="table-layout: fixed;">
              <thead class="sticky top-0 z-20">
                <tr class="bg-emerald-800 text-white font-extrabold uppercase text-[10px] tracking-wider border-b-2 border-emerald-900 select-none">
                  <th class="py-3.5 px-3 border-r border-emerald-700/80 w-16 text-center font-mono">#</th>
                  <th class="py-3.5 px-3 border-r border-emerald-700/80 w-44">N° Identificación</th>
                  <th class="py-3.5 px-3 border-r border-emerald-700/80 w-36">Primer Nombre</th>
                  <th class="py-3.5 px-3 border-r border-emerald-700/80 w-36">Segundo Nombre</th>
                  <th class="py-3.5 px-3 border-r border-emerald-700/80 w-56">Correo Personal</th>
                  <th class="py-3.5 px-3 border-r border-emerald-700/80 w-32">Teléfono</th>
                  <th class="py-3.5 px-3 border-r border-emerald-700/80 w-36">Cargo</th>
                  <th class="py-3.5 px-3 border-r border-emerald-700/80 w-40">Tipo Vinculación</th>
                  <th class="py-3.5 px-3 border-r border-emerald-700/80 w-40">Departamento</th>
                  <th class="py-3.5 px-3 border-r border-emerald-700/80 w-36">Centro de Costo</th>
                  <th class="py-3.5 px-3 border-r border-emerald-700/80 w-32">Sede</th>
                  <th class="py-3.5 px-2 w-10 text-center"></th>
                </tr>
              </thead>
              <tbody>
                <tr :style="{ height: paddingTop + 'px' }"></tr>
                <tr 
                  v-for="item in visibleRows" 
                  :key="item.index" 
                  class="hover:bg-emerald-100/50 transition group h-[36px] border-b border-slate-300"
                >
                  <td class="py-1 px-3 border-r border-slate-300 text-center font-mono text-emerald-900 bg-slate-200/60 font-bold">
                    {{ item.index + 1 }}
                  </td>
                  <td class="p-0 border-r border-slate-300"><input v-model="item.row.identificacion" type="text" placeholder="Sin datos..." class="table-input font-mono font-semibold" /></td>
                  <td class="p-0 border-r border-slate-300"><input v-model="item.row.primerNombre" type="text" placeholder="Primer nombre" class="table-input font-medium" /></td>
                  <td class="p-0 border-r border-slate-300"><input v-model="item.row.segundoNombre" type="text" placeholder="Segundo nombre" class="table-input" /></td>
                  <td class="p-0 border-r border-slate-300"><input v-model="item.row.correoPersonal" type="text" placeholder="correo@ejemplo.com" class="table-input font-mono text-emerald-900 font-semibold" /></td>
                  <td class="p-0 border-r border-slate-300"><input v-model="item.row.telefono" type="text" placeholder="3000000000" class="table-input font-mono" /></td>
                  <td class="p-0 border-r border-slate-300"><input v-model="item.row.cargo" type="text" placeholder="Cargo..." class="table-input" /></td>
                  <td class="p-0 border-r border-slate-300"><input v-model="item.row.tipoVinculacion" type="text" placeholder="Vinculación..." class="table-input font-bold text-emerald-900" /></td>
                  <td class="p-0 border-r border-slate-300"><input v-model="item.row.departamento" type="text" placeholder="Departamento..." class="table-input" /></td>
                  <td class="p-0 border-r border-slate-300"><input v-model="item.row.centroCosto" type="text" placeholder="Centro de costo..." class="table-input" /></td>
                  <td class="p-0 border-r border-slate-300"><input v-model="item.row.sede" type="text" placeholder="Sede..." class="table-input" /></td>
                  <td class="p-1 text-center border-r border-slate-300">
                    <button @click="removeRow('add', item.index)" class="text-slate-400 hover:text-rose-600 font-bold p-1 rounded opacity-0 group-hover:opacity-100 transition">✕</button>
                  </td>
                </tr>
                <tr :style="{ height: paddingBottom + 'px' }"></tr>
              </tbody>
            </table>
          </div>

          <div class="mt-6 flex flex-col sm:flex-row items-center justify-between gap-4">
            <p class="text-xs text-slate-600 font-semibold">⚡ Salida en formato JSON listo para API backend.</p>
            <button @click="submitModule('add')" :disabled="loading || addRows.length === 0" class="w-full sm:w-auto px-8 py-3 bg-emerald-700 hover:bg-emerald-800 text-white font-black text-xs uppercase tracking-wider rounded-xl shadow-lg transition active:scale-95 disabled:opacity-40">
              <span v-if="loading">Procesando...</span>
              <span v-else>Registrar {{ addRows.length.toLocaleString() }} Usuario(s)</span>
            </button>
          </div>
          <div v-if="successMessage" class="mt-4 p-4 bg-emerald-100 border border-emerald-300 text-emerald-900 text-xs rounded-xl font-bold">✨ {{ successMessage }}</div>
        </section>

        <!-- ==================== VISTA 2: ELIMINAR USUARIO ==================== -->
        <section v-if="activeTab === 'delete'" class="bg-slate-100 border border-slate-300 rounded-2xl shadow-md p-6">
          <div class="flex flex-col md:flex-row md:items-center justify-between pb-4 border-b border-slate-300 gap-4 mb-6">
            <div>
              <h2 class="text-lg font-black text-slate-900">Eliminación Masiva de Usuarios</h2>
              <p class="text-xs text-slate-600 mt-0.5">Pega la lista de correos directamente desde Excel (<kbd class="px-2 py-0.5 bg-slate-200 border border-slate-300 rounded text-[10px] font-mono font-bold text-slate-800">Ctrl + V</kbd>).</p>
            </div>
            <div class="flex items-center gap-2">
              <button @click="addRow('delete')" class="px-4 py-2 bg-rose-700 hover:bg-rose-800 text-white font-bold text-xs rounded-xl shadow-md transition active:scale-95">+ Nueva Fila</button>
              <button @click="clearAll('delete')" class="px-4 py-2 bg-slate-200 hover:bg-slate-300 text-slate-700 border border-slate-300 rounded-xl text-xs font-bold transition">Limpiar</button>
            </div>
          </div>

          <div 
            class="overflow-x-auto overflow-y-auto rounded-xl border border-slate-300 bg-slate-50 shadow-xs h-[500px] relative"
            @scroll="onScroll"
            @paste="(e) => handlePaste(e, 'delete')"
          >
            <table class="w-full border-collapse text-xs text-left">
              <thead class="sticky top-0 z-20">
                <tr class="bg-rose-800 text-white font-extrabold uppercase text-[10px] tracking-wider border-b-2 border-rose-900 select-none">
                  <th class="py-3.5 px-3 border-r border-rose-700/80 w-16 text-center font-mono">#</th>
                  <th class="py-3.5 px-3 border-r border-rose-700/80">Correo Institucional</th>
                  <th class="py-3.5 px-2 w-10 text-center"></th>
                </tr>
              </thead>
              <tbody>
                <tr :style="{ height: paddingTop + 'px' }"></tr>
                <tr 
                  v-for="item in visibleRows" 
                  :key="item.index" 
                  class="hover:bg-rose-50/60 transition group h-[36px] border-b border-slate-300"
                >
                  <td class="py-1 px-3 border-r border-slate-300 text-center font-mono text-rose-900 bg-slate-200/60 font-bold">
                    {{ item.index + 1 }}
                  </td>
                  <td class="p-0 border-r border-slate-300">
                    <input v-model="item.row.correo" type="text" placeholder="correo@unicordoba.edu.co" class="table-input font-mono text-rose-950 font-semibold" />
                  </td>
                  <td class="p-1 text-center border-r border-slate-300">
                    <button @click="removeRow('delete', item.index)" class="text-slate-400 hover:text-rose-600 font-bold p-1 rounded opacity-0 group-hover:opacity-100 transition">✕</button>
                  </td>
                </tr>
                <tr :style="{ height: paddingBottom + 'px' }"></tr>
              </tbody>
            </table>
          </div>

          <div class="mt-6 flex flex-col sm:flex-row items-center justify-between gap-4">
            <p class="text-xs text-slate-600 font-semibold">⚡ Genera objeto JSON con propiedad 'emails'.</p>
            <button @click="submitModule('delete')" :disabled="loading || deleteRows.length === 0" class="w-full sm:w-auto px-8 py-3 bg-rose-700 hover:bg-rose-800 text-white font-black text-xs uppercase tracking-wider rounded-xl shadow-lg transition active:scale-95 disabled:opacity-40">
              <span v-if="loading">Procesando...</span>
              <span v-else>Eliminar {{ deleteRows.length.toLocaleString() }} Usuario(s)</span>
            </button>
          </div>
          <div v-if="successMessage" class="mt-4 p-4 bg-rose-100 border border-rose-300 text-rose-900 text-xs rounded-xl font-bold">✨ {{ successMessage }}</div>
        </section>

        <!-- ==================== VISTA 3: SUSPENDER USUARIO ==================== -->
        <section v-if="activeTab === 'suspend'" class="bg-slate-100 border border-slate-300 rounded-2xl shadow-md p-6">
          <div class="flex flex-col md:flex-row md:items-center justify-between pb-4 border-b border-slate-300 gap-4 mb-6">
            <div>
              <h2 class="text-lg font-black text-slate-900">Suspensión Masiva de Usuarios</h2>
              <p class="text-xs text-slate-600 mt-0.5">Pega la lista de correos directamente desde Excel (<kbd class="px-2 py-0.5 bg-slate-200 border border-slate-300 rounded text-[10px] font-mono font-bold text-slate-800">Ctrl + V</kbd>).</p>
            </div>
            <div class="flex items-center gap-2">
              <button @click="addRow('suspend')" class="px-4 py-2 bg-amber-700 hover:bg-amber-800 text-white font-bold text-xs rounded-xl shadow-md transition active:scale-95">+ Nueva Fila</button>
              <button @click="clearAll('suspend')" class="px-4 py-2 bg-slate-200 hover:bg-slate-300 text-slate-700 border border-slate-300 rounded-xl text-xs font-bold transition">Limpiar</button>
            </div>
          </div>

          <div 
            class="overflow-x-auto overflow-y-auto rounded-xl border border-slate-300 bg-slate-50 shadow-xs h-[500px] relative"
            @scroll="onScroll"
            @paste="(e) => handlePaste(e, 'suspend')"
          >
            <table class="w-full border-collapse text-xs text-left">
              <thead class="sticky top-0 z-20">
                <tr class="bg-amber-800 text-white font-extrabold uppercase text-[10px] tracking-wider border-b-2 border-amber-900 select-none">
                  <th class="py-3.5 px-3 border-r border-amber-700/80 w-16 text-center font-mono">#</th>
                  <th class="py-3.5 px-3 border-r border-amber-700/80">Correo Institucional</th>
                  <th class="py-3.5 px-2 w-10 text-center"></th>
                </tr>
              </thead>
              <tbody>
                <tr :style="{ height: paddingTop + 'px' }"></tr>
                <tr 
                  v-for="item in visibleRows" 
                  :key="item.index" 
                  class="hover:bg-amber-50/60 transition group h-[36px] border-b border-slate-300"
                >
                  <td class="py-1 px-3 border-r border-slate-300 text-center font-mono text-amber-900 bg-slate-200/60 font-bold">
                    {{ item.index + 1 }}
                  </td>
                  <td class="p-0 border-r border-slate-300">
                    <input v-model="item.row.correo" type="text" placeholder="correo@unicordoba.edu.co" class="table-input font-mono text-amber-950 font-semibold" />
                  </td>
                  <td class="p-1 text-center border-r border-slate-300">
                    <button @click="removeRow('suspend', item.index)" class="text-slate-400 hover:text-rose-600 font-bold p-1 rounded opacity-0 group-hover:opacity-100 transition">✕</button>
                  </td>
                </tr>
                <tr :style="{ height: paddingBottom + 'px' }"></tr>
              </tbody>
            </table>
          </div>

          <div class="mt-6 flex flex-col sm:flex-row items-center justify-between gap-4">
            <p class="text-xs text-slate-600 font-semibold">⚡ Genera objeto JSON con propiedad 'emails'.</p>
            <button @click="submitModule('suspend')" :disabled="loading || suspendRows.length === 0" class="w-full sm:w-auto px-8 py-3 bg-amber-700 hover:bg-amber-800 text-white font-black text-xs uppercase tracking-wider rounded-xl shadow-lg transition active:scale-95 disabled:opacity-40">
              <span v-if="loading">Procesando...</span>
              <span v-else>Suspender {{ suspendRows.length.toLocaleString() }} Usuario(s)</span>
            </button>
          </div>
          <div v-if="successMessage" class="mt-4 p-4 bg-amber-100 border border-amber-300 text-amber-900 text-xs rounded-xl font-bold">✨ {{ successMessage }}</div>
        </section>

      </main>
    </div>

  </div>
</template>

<script setup>
import { ref, computed } from 'vue'

const activeTab = ref('add')
const searchQuery = ref('')

// Estado Overlay Carga
const isPasting = ref(false)
const pasteProcessedCount = ref(0)
const pasteTotalCount = ref(0)

const pastePercent = computed(() => {
  if (pasteTotalCount.value === 0) return 0
  return Math.min(100, Math.round((pasteProcessedCount.value / pasteTotalCount.value) * 100))
})

const currentTabTitle = computed(() => {
  if (activeTab.value === 'add') return 'Agregar Usuario'
  if (activeTab.value === 'delete') return 'Eliminar Usuario'
  if (activeTab.value === 'suspend') return 'Suspender Usuario'
  return ''
})

const switchTab = (tab) => {
  activeTab.value = tab
  searchQuery.value = ''
  scrollTop.value = 0
}

// Objetos base
const emptyAddRow = () => ({ identificacion: '', primerNombre: '', segundoNombre: '', correoPersonal: '', telefono: '', cargo: '', tipoVinculacion: '', departamento: '', centroCosto: '', sede: '' })
const emptyDeleteRow = () => ({ correo: '' })
const emptySuspendRow = () => ({ correo: '' })

// Arrays de datos
const addRows = ref([emptyAddRow()])
const deleteRows = ref([emptyDeleteRow()])
const suspendRows = ref([emptySuspendRow()])

const loading = ref(false)
const successMessage = ref('')

const currentRows = computed(() => {
  if (activeTab.value === 'add') return addRows.value
  if (activeTab.value === 'delete') return deleteRows.value
  if (activeTab.value === 'suspend') return suspendRows.value
  return []
})

// Mapeador estricto JSON:
// - Agregar: [{ employeeId: '...', ... }]
// - Eliminar / Suspender: { "emails": ["correo1", "correo2", ...] }
const currentPayload = computed(() => {
  if (activeTab.value === 'add') {
    return addRows.value.map(row => ({
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
  } else {
    const list = activeTab.value === 'delete' ? deleteRows.value : suspendRows.value
    return {
      emails: list.map(row => row.correo).filter(email => email.trim() !== '')
    }
  }
})

// Filtro Buscador
const filteredRows = computed(() => {
  if (!searchQuery.value.trim()) return currentRows.value
  const q = searchQuery.value.toLowerCase().trim()
  
  return currentRows.value.filter(row => {
    return Object.values(row).some(val => String(val).toLowerCase().includes(q))
  })
})

// Virtual Scroll JS
const ROW_HEIGHT = 36 
const VISIBLE_COUNT = 25 
const BUFFER = 10 

const scrollTop = ref(0)

const onScroll = (e) => {
  scrollTop.value = e.target.scrollTop
}

const startIndex = computed(() => {
  return Math.max(0, Math.floor(scrollTop.value / ROW_HEIGHT) - BUFFER)
})

const endIndex = computed(() => {
  return Math.min(filteredRows.value.length, startIndex.value + VISIBLE_COUNT + (BUFFER * 2))
})

const visibleRows = computed(() => {
  return filteredRows.value.slice(startIndex.value, endIndex.value).map((row, i) => ({
    index: startIndex.value + i,
    row
  }))
})

const paddingTop = computed(() => {
  return startIndex.value * ROW_HEIGHT
})

const paddingBottom = computed(() => {
  return Math.max(0, (filteredRows.value.length - endIndex.value) * ROW_HEIGHT)
})

// Pegado masivo 500 en 500
const handlePaste = (event, tab) => {
  event.preventDefault()
  const clipboardData = event.clipboardData || window.clipboardData
  const pastedText = clipboardData.getData('text')
  if (!pastedText) return

  const lines = pastedText.trim().split(/\r\n|\n|\r/)
  const total = lines.length

  isPasting.value = true
  pasteProcessedCount.value = 0
  pasteTotalCount.value = total

  const chunkSize = 500 
  let currentIndex = 0
  let isFirstBatch = true

  const processChunk = () => {
    const end = Math.min(currentIndex + chunkSize, total)
    const chunkLines = lines.slice(currentIndex, end)

    let parsedChunk = []

    if (tab === 'add') {
      parsedChunk = chunkLines.map(line => {
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
      if (isFirstBatch && addRows.value.length === 1 && !addRows.value[0].identificacion) addRows.value = parsedChunk
      else addRows.value.push(...parsedChunk)
    } 
    else if (tab === 'delete') {
      parsedChunk = chunkLines.map(line => ({ correo: line.split('\t')[0]?.trim() || '' }))
      if (isFirstBatch && deleteRows.value.length === 1 && !deleteRows.value[0].correo) deleteRows.value = parsedChunk
      else deleteRows.value.push(...parsedChunk)
    } 
    else if (tab === 'suspend') {
      parsedChunk = chunkLines.map(line => ({ correo: line.split('\t')[0]?.trim() || '' }))
      if (isFirstBatch && suspendRows.value.length === 1 && !suspendRows.value[0].correo) suspendRows.value = parsedChunk
      else suspendRows.value.push(...parsedChunk)
    }

    isFirstBatch = false
    currentIndex = end
    pasteProcessedCount.value = currentIndex

    if (currentIndex < total) {
      setTimeout(processChunk, 2)
    } else {
      setTimeout(() => { isPasting.value = false }, 200)
    }
  }

  processChunk()
}

const addRow = (tab) => {
  if (tab === 'add') addRows.value.push(emptyAddRow())
  if (tab === 'delete') deleteRows.value.push(emptyDeleteRow())
  if (tab === 'suspend') suspendRows.value.push(emptySuspendRow())
}

const removeRow = (tab, index) => {
  if (tab === 'add') addRows.value.splice(index, 1)
  if (tab === 'delete') deleteRows.value.splice(index, 1)
  if (tab === 'suspend') suspendRows.value.splice(index, 1)
}

const clearAll = (tab) => {
  if (tab === 'add') addRows.value = [emptyAddRow()]
  if (tab === 'delete') deleteRows.value = [emptyDeleteRow()]
  if (tab === 'suspend') suspendRows.value = [emptySuspendRow()]
  searchQuery.value = ''
  successMessage.value = ''
  scrollTop.value = 0
}

const submitModule = (tab) => {
  loading.value = true
  successMessage.value = ''

  console.log(JSON.stringify(currentPayload.value, null, 2))

  setTimeout(() => {
    loading.value = false
    const totalCount = activeTab.value === 'add' ? currentPayload.value.length : currentPayload.value.emails.length
    successMessage.value = `¡Proceso completado exitosamente! Se procesaron ${totalCount.toLocaleString()} registro(s) en formato JSON.`
  }, 800)
}
</script>

<style scoped>
.table-input {
  width: 100%;
  padding: 0.5rem 0.75rem;
  background-color: transparent;
  color: #0f172a;
  outline: none;
  font-size: 11px;
  transition: all 0.15s ease;
}
.table-input::placeholder {
  color: #94a3b8;
}
.table-input:focus {
  background-color: #ffffff;
  box-shadow: inset 0 0 0 2px #047857;
}
</style>
<template>
  <div class="p-grid">
      <Toast />
      <div class="p-col-12">
          <div class="card">
              <Panel header="PUNTO DE VENTA (POS)" style="height: 100%">
                  <Toolbar class="p-mb-4">
                      <template v-slot:start>
                          <InputText type="text" size="40" placeholder="Nombre del producto..." v-model="productoItem.nomProducto" />
                          <InputText class="ml-8" type="text" placeholder="Cant" v-model="productoItem.cantidad" />
                          <InputText class="ml-8" type="text" placeholder="$ Precio U." v-model="productoItem.precioUnitario" />
                      </template>
                      <template v-slot:end>
                          <Button label="Registrar" icon="pi pi-plus" class="p-button-success p-mr-2" @click="registrarCompra()" />
                      </template>
                  </Toolbar>
                  <br>

                  <DataTable :value="tablaCompras" class="p-datatable-gridlines" dataKey="cns" :rows="5" paginator responsiveLayout="scroll">
                      <Column field="cns" header="Cns" style="width:50px"></Column>
                      <Column field="nomProducto" header="Nombre del Producto" style="width:370px"></Column>
                      <Column field="precioUnitario" header="Precio U." style="width:80px">
                          <template #body="slotProps">
                              {{ formatoMoneda(slotProps.data.precioUnitario) }}
                          </template>
                      </Column>
                      <Column field="cantidad" header="Cant." style="width:60px; text-align:center"></Column>
                      <Column field="precioParcial" header="Precio P." style="width:80px">
                          <template #body="slotProps">
                              {{ formatoMoneda(slotProps.data.precioParcial) }}
                          </template>
                      </Column>
                      <Column style="width:140px">
                          <template #header> Acciones </template>
                          <template #body="slotProps">
                              <Button icon="pi pi-pencil" class="p-button-success p-mr-2" @click="editarProducto(slotProps.data)"></Button>
                              <Button icon="pi pi-trash" class="p-button-danger" @click="eliminarProducto(slotProps.data)"></Button>
                          </template>
                      </Column>
                  </DataTable>

                  <!-- Totales -->
                  <div class="p-grid p-justify-end p-mt-4">
                      <div class="p-col-12 p-md-4">
                          <div class="p-d-flex p-jc-between">
                              <div><strong>Subtotal:</strong></div>
                              <div>{{ formatoMoneda(subtotal) }}</div>
                          </div>
                          <div class="p-d-flex p-jc-between">
                              <div><strong>IVA (16%):</strong></div>
                              <div>{{ formatoMoneda(iva) }}</div>
                          </div>
                          <div class="p-d-flex p-jc-between">
                              <div><strong>Total:</strong></div>
                              <div>{{ formatoMoneda(total) }}</div>
                          </div>
                      </div>
                  </div>

                  <!-- Modal for Update -->
                  <Dialog v-model:visible="visibleUpdate" modal header="Actualizar datos de un producto" :style="{ width: '45vw' }">
                      <div class="flex gap-2">
                          <div class="flex-auto">
                              <label><strong>Nombre del producto:</strong></label>
                              <InputText class="ml-2" v-model="nomp" />
                          </div>
                          <div class="flex-auto">
                              <label><strong>Precio Unitario:</strong></label>
                              <InputText class="ml-2" v-model="precioUnitario" />
                          </div>
                          <div class="flex-auto">
                              <label><strong>Cantidad:</strong></label>
                              <InputText class="ml-2" v-model="cantidad" />
                          </div>
                      </div>
                      <template #footer>
                          <Button label="Actualizar" icon="pi pi-replay" @click="updateProducto()" />
                      </template>
                  </Dialog>

                  <!-- Modal for Delete Confirmation -->
                  <Dialog v-model:visible="visibleDelete" modal header="¿Estás seguro que quieres borrar este producto?" :style="{ width: '30vw' }">
                      <p>Confirma si deseas eliminar este producto.</p>
                      <template #footer>
                          <Button label="Eliminar" icon="pi pi-check" class="p-button-danger" @click="deleteProducto()" />
                          <Button label="Cancelar" icon="pi pi-times" class="p-button-secondary" @click="visibleDelete = false" />
                      </template>
                  </Dialog>
              </Panel>
          </div>
      </div>
  </div>
</template>

<script>
import { useToast } from 'primevue/usetoast';
import { ref, computed } from 'vue';

export default {
  setup() {
      const toast = useToast();
      const visibleUpdate = ref(false);
      const visibleDelete = ref(false);

      const nomp = ref('');
      const cantidad = ref('');
      const precioUnitario = ref('');
      const productoSeleccionado = ref(null);

      const tablaCompras = ref([
          { cns: 1, nomProducto: "Impresora LaserJet Color", cantidad: 2, precioUnitario: 5200.00, precioParcial: 10400.00 },
          { cns: 2, nomProducto: "Monitor LED 31 plg.", cantidad: 3, precioUnitario: 1700.00, precioParcial: 5100.00 }
      ]);

      const productoItem = ref({
          cns: null,
          nomProducto: '',
          cantidad: '',
          precioUnitario: ''
      });

      const subtotal = computed(() =>
          tablaCompras.value.reduce((acc, item) => acc + item.precioParcial, 0)
      );

      const iva = computed(() => subtotal.value * 0.16);
      const total = computed(() => subtotal.value + iva.value);

      function registrarCompra() {
          if (
              productoItem.value.nomProducto.trim() !== '' &&
              productoItem.value.cantidad !== '' &&
              productoItem.value.precioUnitario !== ''
          ) {
              tablaCompras.value.push({
                  cns: tablaCompras.value.length + 1,
                  nomProducto: productoItem.value.nomProducto,
                  cantidad: parseFloat(productoItem.value.cantidad),
                  precioUnitario: parseFloat(productoItem.value.precioUnitario),
                  precioParcial: parseFloat(productoItem.value.cantidad) * parseFloat(productoItem.value.precioUnitario)
              });
              toast.add({ severity: 'success', summary: 'Confirmación', detail: 'Nueva compra registrada', life: 3000 });

              productoItem.value.nomProducto = '';
              productoItem.value.cantidad = '';
              productoItem.value.precioUnitario = '';
          } else {
              toast.add({ severity: 'warn', summary: 'Advertencia', detail: 'Todos los campos son requeridos', life: 3000 });
          }
      }

      function eliminarProducto(row) {
          productoSeleccionado.value = row;
          visibleDelete.value = true;
      }

      function deleteProducto() {
          const index = tablaCompras.value.findIndex(producto => producto.cns === productoSeleccionado.value.cns);
          if (index !== -1) {
              tablaCompras.value.splice(index, 1);
              toast.add({ severity: 'info', summary: 'Eliminado', detail: 'Producto eliminado correctamente', life: 3000 });
          }
          visibleDelete.value = false;
      }

      function editarProducto(row) {
          productoSeleccionado.value = row;
          nomp.value = row.nomProducto;
          cantidad.value = row.cantidad;
          precioUnitario.value = row.precioUnitario;
          visibleUpdate.value = true;
      }

      function updateProducto() {
          if (productoSeleccionado.value) {
              productoSeleccionado.value.nomProducto = nomp.value;
              productoSeleccionado.value.cantidad = cantidad.value;
              productoSeleccionado.value.precioUnitario = precioUnitario.value;
              productoSeleccionado.value.precioParcial = cantidad.value * precioUnitario.value;

              toast.add({ severity: 'success', summary: 'Actualización', detail: 'Producto actualizado correctamente', life: 3000 });
              visibleUpdate.value = false;
          }
      }

      function formatoMoneda(value) {
          if (value) return value.toLocaleString('en-US', { style: 'currency', currency: 'USD' });
          return '';
      }

      return {
          toast,
          visibleUpdate,
          visibleDelete,
          nomp,
          cantidad,
          precioUnitario,
          productoItem,
          tablaCompras,
          registrarCompra,
          eliminarProducto,
          deleteProducto,
          editarProducto,
          updateProducto,
          formatoMoneda,
          subtotal,
          iva,
          total
      };
  }
};
</script>

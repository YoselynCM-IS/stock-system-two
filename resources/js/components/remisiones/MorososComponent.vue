<template>
    <div>
        <b-row>
            <b-col sm="6">
                <!-- PAGINACIÓN -->
                <pagination size="default" :limit="1" :data="remisiones" 
                    @pagination-change-page="getResults">
                    <span slot="prev-nav"><i class="fa fa-angle-left"></i></span>
                    <span slot="next-nav"><i class="fa fa-angle-right"></i></span>
                </pagination>
            </b-col>
            <b-col>
                <!-- BUSQUEDA POR CLIENTE -->
                <search-select-cliente-component :titulo="'Buscar cliente:'" :load="load" @sendCliente="sendCliente"></search-select-cliente-component>
            </b-col>
            <b-col>
                <!-- BUSQUEDA POR RANGO DE TIEMPO -->
                <b-form-group label="Rango de tiempo" label-class="font-weight-bold">
                    <b-form-select v-model="type" :options="options" 
                            :disabled="load" @change="searchRangoFecha()"></b-form-select>
                </b-form-group>
            </b-col>
        </b-row>
        <!-- TABLA DE REMISIONES -->
        <b-table v-if="remisiones.data" :items="remisiones.data" :fields="fields">
            <template v-slot:cell(total)="row">
                ${{ row.item.total | formatNumber }}
            </template>
            <template v-slot:cell(total_devolucion)="row">
                ${{ row.item.total_devolucion | formatNumber }}
            </template>
            <template v-slot:cell(pagos)="row">
                ${{ row.item.pagos | formatNumber }}
            </template>
            <template v-slot:cell(total_pagar)="row">
                ${{ row.item.total_pagar | formatNumber }}
            </template>
        </b-table>
        <no-registros-component v-else></no-registros-component>
    </div>
</template>

<script>
import SearchSelectClienteComponent from '../funciones/SearchSelectClienteComponent.vue';
import NoRegistrosComponent from '../funciones/NoRegistrosComponent.vue';
import formatNumber from '../../mixins/formatNumber';

export default {
    components: { SearchSelectClienteComponent, NoRegistrosComponent },
    mixins: [formatNumber],
    data() {
        return {
            type: null,
            cliente_id: null,
            options: [
                { value: null, text: 'Selecciona una opción', disabled: true },
                { value: [0, 30], text: '0 a 30 días' },
                { value: [31, 60], text: '31 a 60 días' },
                { value: [61, 90], text: '61 a 90 días' },
                { value: [91, 120], text: '91 a 120 días' },
                { value: [121, 150], text: '121 a 150 días' },
                { value: [151, 9999], text: 'Mas de 150 días' },
            ],
            load: false,
            remisiones: {},
            fields: [
                { key: 'id', label: 'Folio' },
                { key: 'fecha_creacion', label: 'Fecha de creación' },
                { key: 'cliente.name', label: 'Cliente' },
                { key: 'total', label: 'Salida' },
                'pagos',
                { key: 'total_devolucion', label: 'Devolución' },
                { key: 'total_pagar', label: 'Pagar' },
                { key: 'detalles', label: '' }
            ]
        }
    },
    methods: {
        getResults(page = 1) {
            this.searchRangoFecha(page);
        },
        // BUSQUEDA DE REMISIONES
        searchRangoFecha(page = 1) {
            this.load = true;
            axios.get(`/remisiones/morosos/by_rangofecha?page=${page}`,
                { params: { type: this.type, cliente_id: this.cliente_id } }).then(response => {
                    this.remisiones = response.data;
                    // console.log(response.data);
                this.load = false;
            }).catch(error => {
                this.load = false;
            });
        },
        sendCliente(cliente) {
            this.cliente_id = cliente.id;
        }
    }
}
</script>

<style>

</style>
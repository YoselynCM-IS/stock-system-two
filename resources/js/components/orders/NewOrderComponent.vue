<template>
    <div>
        <b-row>
            <b-col>
                <b-form-group label="PROVEEDOR:" label-class="font-weight-bold">
                    <b-form-select v-model="form.editorial" autofocus 
                        :disabled="load" :options="options">
                    </b-form-select>
                </b-form-group> 
            </b-col>
            <b-col>
                <search-select-cliente-component :titulo="'PARA:'" :load="load" @sendCliente="sendCliente"></search-select-cliente-component>
            </b-col>
            <b-col sm="2">
                <b-button @click="save_pedido()" class="mt-2" variant="success" pill block
                    :disabled="(load || this.form.libros.length == 0 || this.form.cliente_id == null || form.editorial == null)">
                    <i class="fa fa-check-circle"></i> Guardar
                </b-button>
            </b-col>
        </b-row>
        <table-pedidos-component :load="load" @sendPedidos="sendPedidos"></table-pedidos-component>>
    </div>
</template>

<script>
import TablePedidosComponent from '../funciones/pedidos/TablePedidosComponent.vue';
import SearchSelectClienteComponent from '../funciones/SearchSelectClienteComponent.vue'
import getEditoriales from '../../mixins/getEditoriales';
export default {
    mixins: [getEditoriales],
    components: { SearchSelectClienteComponent, TablePedidosComponent },
    data(){
        return {
            load: false,
            form: {
                editorial: null,
                cliente_id: null,
                cliente_name: null,
                total_bill: 0,
                libros: []
            }
        }
    },
    created: function(){
        this.get_editoriales();
    },
    methods: {
        sendCliente(cliente){
            this.form.cliente_id = cliente.id;
            this.form.cliente_name = cliente.name;
        },
        sendPedidos(form){
            this.form.libros = form.libros;
            this.form.total_bill = form.total;
        },
        save_pedido(){
            this.load = true;
            axios.post('/order/store', this.form).then(response => {
                swal("OK", "El pedido se guardo correctamente.", "success")
                    .then((value) => { location.reload(); });
                this.load = false;
            }).catch(error => {
                this.load = false;
            });
        }
    }
}
</script>

<style>

</style>
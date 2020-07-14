<template>
    <div style="color:#979797">
        <div style="text-align: center;"><b style="font-size:24pt" >My Order</b>
        </div>
        <div class="order" v-for="(order, index) in orders" :key="`${index}-${order.id}`" >Order code:{{order.code_order}}
            <button v-if="order.status != 0" style="margin-left: 500px" @click="deleteOrder(order.id, index)" class="btn btn-outline-danger">Delete</button>
            <div style="clear: both;"></div>
            <div style="width: 100%" >
                <div class="order-detail" v-for="(orderdetail, index_orderdetail) in order.order_detail" :key="`${index_orderdetail}-${orderdetail.id}`">
                    <img :src="orderdetail.image" class="img_orderdetail">
                    <div class="order_content">{{orderdetail.product.name}}</div>
                    <span style="font-size: 14px;margin-right: 24px">Qty:{{orderdetail.quantity}}</span>
                    <div style="clear: both;"></div>
                </div>
            </div>

        </div>

    </div>
</template>

<script>
    export default {
        name: "Order",
        data() {
            return {
                editMode: false,
                orders:[],
            }
        },
        methods: {
            getAllOrderOfAccount() {
                Fire.$emit('onLoading');
                axios.get('/api/getAllOrderOfAccount')
                .then(response => {
                    Fire.$emit('offLoading');
                    console.log(response.data);
                    this.orders = response.data;
                })
            },
            deleteOrder(id, index) {
                Swal.fire({
                    title: 'Are you sure?',
                    text: "You won't be able to revert this!",
                    icon: 'warning',
                    showCancelButton: true,
                    confirmButtonColor: '#3085d6',
                    cancelButtonColor: '#d33',
                    confirmButtonText: 'Yes, delete it!'
                }).then((result) => {
                    if (result.value) {
                        Swal.fire(
                            'Deleted!',
                            'Your file has been deleted.',
                            'success'
                        );
                        var app = this;
                        axios.delete('/api/deleteOrder/' + id)
                        .then(function (resp) {
                            Fire.$emit('loadOrder');
                        })
                        .catch(function (resp) {
                            alert("Could not delete company");
                            console.log(resp)
                        });
                    }

                })

            },
        },
        created() {

            this.getAllOrderOfAccount();
            Fire.$on('loadOrder', ()=>{
                this.getAllOrderOfAccount();
            });
        }
    }
</script>

<style scoped>

</style>

<template>
    <div id="app">
        <ejs-button cssClass='e-success' @click="click">Bind data via fetch</ejs-button>
        <div style="padding: 20px 17px 0 0">
            <ejs-grid ref="grid" :editSettings='editSettings' :toolbar='toolbar' allowPaging="true" height="320" :actionBegin="actionBegin" :actionComplete="actionComplete">
                <e-columns>
                    <e-column field='OrderID' headerText='Order ID' isPrimaryKey=true width='150'></e-column>
                    <e-column field='CustomerID' headerText='Customer Name' width='150'></e-column>
                    <e-column field='Freight' headerText='Freight' format="C" width='150'></e-column>
                    <e-column field='ShipCity' headerText='ShipCity' width='150' textAlign='Right'></e-column>
                </e-columns>
            </ejs-grid>
        </div>
    </div>
</template>

<script>
    import { GridComponent, ColumnsDirective, ColumnDirective, Page, Edit, Toolbar } from "@syncfusion/ej2-vue-grids";
    import { ButtonComponent } from "@syncfusion/ej2-vue-buttons";
    import { Fetch } from '@syncfusion/ej2-base';

    export default {
        name: "App",
        components: {
            "ejs-grid": GridComponent,
            "e-columns": ColumnsDirective,
            "e-column": ColumnDirective,
            "ejs-button": ButtonComponent
        },
        data() {
            return {
                flag: false,
                editSettings: { allowEditing: true, allowAdding: true, allowDeleting: true, mode: 'Normal' },
                toolbar: ['Add', 'Edit', 'Delete', 'Update', 'Cancel']
            };
        },
        methods: {
            click: function () {
                const fetchRequest = new Fetch("https://localhost:7176/Home/Getdata", 'POST'); //Use remote server host number instead 7176
                fetchRequest.send();
                fetchRequest.onSuccess = (data) => {
                    this.$refs.grid.ej2Instances.dataSource = data;
                };
            },
            actionBegin: function (e) {
                if (!this.flag) {
                    if (e.requestType == 'save' && (e.action == 'add')) {
                        var editedData = e.data;
                        e.cancel = true;
                        var fetchRequest = new Fetch({
                            url: 'https://localhost:7176/Home/Insert',
                            type: 'POST',
                            contentType: 'application/json; charset=utf-8',
                            data: JSON.stringify({ value: editedData })
                        });
                        fetchRequest.onSuccess = () => {
                            this.flag = true;
                            this.$refs.grid.ej2Instances.endEdit();
                        };
                        fetchRequest.onFailure = () => {
                            this.flag = false;
                        };
                        fetchRequest.send();
                    }
                    if (e.requestType == 'save' && (e.action == "edit")) {
                        var editedData = e.data;
                        e.cancel = true;
                        var fetchRequest = new Fetch({
                            url: 'https://localhost:7176/Home/Update',
                            type: 'POST',
                            contentType: 'application/json; charset=utf-8',
                            data: JSON.stringify({ value: editedData })
                        });
                        fetchRequest.onSuccess = () => {
                            this.flag = true;
                            this.$refs.grid.ej2Instances.endEdit();
                        };
                        fetchRequest.onFailure = () => {
                            this.flag = false;
                        };
                        fetchRequest.send();
                    }
                    if (e.requestType == 'delete') {
                        var editedData = e.data;
                        e.cancel = true;
                        var fetchRequest = new Fetch({
                            url: 'https://localhost:7176/Home/Delete',
                            type: 'POST',
                            contentType: 'application/json; charset=utf-8',
                            data: JSON.stringify({ key: editedData[0][this.$refs.grid.ej2Instances.getPrimaryKeyFieldNames()[0]] })
                        });
                        fetchRequest.onSuccess = () => {
                            this.flag = true;
                            this.$refs.grid.ej2Instances.deleteRecord();
                        };
                        fetchRequest.onFailure = () => {
                            this.flag = false;
                        };
                        fetchRequest.send();
                    }
                }
            },
            actionComplete: function (e) {
                if (e.requestType === 'save' || e.requestType === 'delete') {
                    this.flag = false;
                }
            }
        },
        provide: {
            grid: [Page, Edit, Toolbar]
        },
    };
</script>

<style>
    @import "../node_modules/@syncfusion/ej2-base/styles/tailwind.css";
    @import "../node_modules/@syncfusion/ej2-buttons/styles/tailwind.css";
    @import "../node_modules/@syncfusion/ej2-calendars/styles/tailwind.css";
    @import "../node_modules/@syncfusion/ej2-dropdowns/styles/tailwind.css";
    @import "../node_modules/@syncfusion/ej2-inputs/styles/tailwind.css";
    @import "../node_modules/@syncfusion/ej2-navigations/styles/tailwind.css";
    @import "../node_modules/@syncfusion/ej2-popups/styles/tailwind.css";
    @import "../node_modules/@syncfusion/ej2-splitbuttons/styles/tailwind.css";
    @import "../node_modules/@syncfusion/ej2-vue-grids/styles/tailwind.css";
</style>
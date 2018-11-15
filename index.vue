<template>
    <div style="margin:20px;">
        <Table border ref="selection" :columns="columns" :data="data" v-on:on-select="getSelectData" v-on:on-select-cancel="removeData" :height="tableHight">
        <div slot="footer" class="footer-row">
            <div class="total-row"> 汇总：{{total}}</div>
        </div>
        </Table>

        <div class="button-group">
        <ButtonGroup style="margin-top: 10px">
           <!-- <Button type="primary" @click="handleSelectAll(true)">全选</Button>
            <Button type="info" @click="handleSelectAll(false)">全都不选</Button>
            <Button type="primary" icon="plus"  title="获取tableData" @click="handlSelectAll">获取tableData</Button>-->
            <Button type="primary" icon="ios-add-circle-outline"  title="新增行" @click="handleAdd">新增</Button>
            <Button type="info" icon="md-arrow-up" title="上移" @click.native="handleUp">上移</Button>
            <Button type="primary" icon="md-arrow-down"  title="下移" @click.native="handleDown">下移</Button>
        </ButtonGroup>
        </div>
    </div>
</template>
<script>
    export default {
        data () {
            return {
                total: '1000千万(人民币)',
                uniqueKey: 0,
                rowData:[],
                tableHight: '150',
                selectData: [
                    {
                        id: 0,
                        text: '是'},
                    {
                        id: 1,
                        text: '否'
                    }
                ],
                columns: [
                    {
                        type: 'selection',
                        width: 60,
                        align: 'center'
                    },
                    {
                        title: '序号',
                        key: 'rowIndex'
                    },
                    {
                        title: '第一列',
                        key: 'col1',
                        render: (h, params) => {
                            return h('div', [
                                h('Icon', {
                                    props: {
                                        type: 'person'
                                    }
                                }),
                                h('strong', params.row.col1)
                            ]);
                        }
                    },
                    {
                        title: '第二列',
                        key: 'col2',
                        render: (h,params) => {
                            return h('Select',{
                                props:{
                                    value: this.data[params.index].col2
                                },
                                on:{
                                    'on-change': (event) => {
                                        console.log(event)
                                        this.data[params.index].col2 = event
                                    }
                                }
                            },
                            [
                                h('Option',{
                                    props:{
                                        value: '0'
                                    }
                                },'男'),
                                    h('Option',{
                                        props:{
                                            value: '1'
                                        }
                                    },'女')
                                    ]
                            )
                        }
                    },
                    {
                        title: '第三例',
                        key: 'col3',
                        render: (h, params) => {
                            return h('Input',{
                                    props: {
                                        type:'text',
                                        value: this.data[params.index].col3
                                    },
                                    style:{
                                        width: 80
                                    },
                                    on:{
                                        'on-blur': (em)=>{
                                            this.data[params.index].col3 = em.target.value
                                        }
                                    }
                                })
                        }
                    },
                    {
                        title: '操作',
                        key: 'action',
                        width: 150,
                        align: 'center',
                        render: (h, params) => {
                            return h('div', [
                                h('Button', {
                                    props: {
                                        type: 'primary',
                                        size: 'small'
                                    },
                                    style: {
                                        marginRight: '5px'
                                    },
                                    on: {
                                        click: () => {
                                            this.addRow(params)
                                        }
                                    }
                                }, '复制'),
                                h('Button', {
                                    props: {
                                        type: 'error',
                                        size: 'small'
                                    },
                                    on: {
                                        click: () => {
                                            this.remove(params.index)
                                        }
                                    }
                                }, '删除')
                            ]);
                        }
                    }
                ],
                data: [
                    {
                        col0: 0,
                        rowIndex:1,
                        col1: 'Table Demo',
                        col2: '',
                        col3: ''
                    }
                ]
            }
        },
        methods: {
            addRow(rowData){
                this.uniqueKey ++
                let rowIndex = 0
                if(this.data.length ==0){
                    rowIndex = 1;
                }else {
                    rowIndex = parseInt(this.data[this.data.length-1].rowIndex) + 1
                }
                let em = {
                    col0: this.uniqueKey,
                    rowIndex: rowIndex,
                    col1: rowData.row.col1,
                    col2: rowData.row.col2,
                    col3: rowData.row.col3
                }
                console.log(em)
                this.data.push(em)
                this.setTableHeight()
            },
            handleAdd() {
                this.uniqueKey ++
                let rowIndex = 0
                if(this.data.length ==0){
                    rowIndex = 1;
                }else {
                    rowIndex = parseInt(this.data[this.data.length-1].rowIndex) + 1
                }
                let item = {
                    col0: this.uniqueKey,
                    rowIndex:rowIndex,
                    col1: '',
                    col2: '',
                    col3: ''
                }
                this.data.push(item)
                this.setTableHeight()
            },
            remove (index) {
                this.data.splice(index, 1);
                this.reduceTableHeight()
                this.reorderIndex()
            },
            handleSelectAll (status) {
                this.$refs.selection.selectAll(status);
            },
            handlSelectAll () {
                console.log('Get the Table  value')
                console.log(this.data)
            },
            handleUp() {
                 // var { ...temp } = this.data[tempIndex]//深拷贝 扩展运算符
                this.exchangeData('up')
            },
            handleDown () {
                this.exchangeData('down')
              console.log('handleDown')
                console.log(this.$refs.selection)
            },
            getSelectData (selection,row) {
                console.log(selection)
                console.log(row)
                this.rowData = selection
            },
            removeData (selection,row) {
                this.rowData = selection;
            },
            getRowIndex (index) {
                let rowIndex = 0
                // 获取当前选中的下标
                for (let i in this.data) {
                    if (this.data[i].col0 === index) {
                        rowIndex = i;
                        break;
                    }
                }
                return rowIndex;
            },
            setTableHeight() {
                this.tableHight =  (parseInt(this.tableHight) + 45) + ''
            },
            reduceTableHeight() {
                this.tableHight =  (parseInt(this.tableHight) - 45) + ''
            },
            exchangeData (flag){
                if (this.rowData.length === 0 || this.rowData.length > 1) {
                    alert('请选择一行')
                    return;
                }
                let index = this.rowData[0].col0
                let rowIndex = this.getRowIndex(index)
                rowIndex = parseInt(rowIndex)
                let tempIndex = rowIndex
                let compareIndex = 0
                if('up'.indexOf(flag)>-1){
                    // 上移动
                    tempIndex = rowIndex -1
                    compareIndex = 0
                }else{
                    // 下移动
                    tempIndex = rowIndex + 1
                    compareIndex = this.data.length -1
                }
                if(rowIndex === compareIndex){
                    return
                }
                let tempData = JSON.parse(JSON.stringify(this.data[tempIndex]))
                this.data[tempIndex].col0 = this.rowData[0].col0
                this.data[tempIndex].col1 = this.rowData[0].col1
                this.data[tempIndex].col2= this.rowData[0].col2
                this.data[tempIndex].col3 = this.rowData[0].col3
                this.data[rowIndex].col0 = tempData.col0
                this.data[rowIndex].col1 = tempData.col1
                this.data[rowIndex].col2 = tempData.col2
                this.data[rowIndex].col3 = tempData.col3
            },
            reorderIndex(){
                for(let i in this.data) {
                    this.data[i].rowIndex = parseInt(i) + 1
                }
            }
        }
    }
</script>
<style>
    .button-group{
        width: 100%;
        align-items: center;
        text-align: center;
    }
    .footer-row{
        height: 40px;
        border:solid 1px #e2e2e6;
        text-align: right;
    }
    .total-row{
        margin-right: 10%;
    }
    .ivu-table-footer, .ivu-table-title{
        height: 40px !important;
        line-height: 40px !important;
    }
</style>

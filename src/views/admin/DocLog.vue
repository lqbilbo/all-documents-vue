<template>
    <div class="main" ref="tableRef">
        <Table ref="logTable" width="100%" :height="height" border :columns="columns" :data="data">
            <template #action="{ row, index }">
                <Button type="error" size="small" @click="remove(index)">删 除</Button>
            </template>
        </Table>

        <div class="bottom-zone">
            <Row>
                <Col span="12" class="bottom-zone-left">
                    <Button type="primary" ghost @click="remove">全部删除</Button>
                </Col>
                <Col span="12" class="bottom-zone-right">
                    <Page
                        :model-value="currentPage"
                        :total="totalItems"
                        :page-size="pageSize"
                        @on-change="pageChange"
                    />
                </Col>
            </Row>
        </div>
    </div>
</template>
<script>
import {resolveComponent} from 'vue'

import {parseTime} from "@/utils"
import docLogRequest from '@/api/docLog'

export default {
    data() {
        return {
            columns: [
                {
                    type: 'selection',
                    width: 60,
                    align: 'center',
                    fixed: "left"
                },
                {
                    title: '时间',
                    key: 'time',
                    width: 200,
                    fixed: 'left'
                },
                {
                    title: '用户',
                    key: 'user',
                    width: 200
                },
                {
                    title: '动作',
                    key: 'action',
                    width: 180
                },
                {
                    title: '文档名称',
                    key: 'name',
                    // width: 300
                },
                {
                    title: '操作',
                    slot: 'action',
                    fixed: 'right',
                    width: 90
                }
            ],
            data: [
                {
                    name: 'John Brown',
                    age: 18,
                    address: 'New York No. 1 Lake Park',
                    province: 'America',
                    city: 'New York',
                    zip: 100000
                },
                {
                    name: 'Jim Green',
                    age: 24,
                    address: 'Washington, D.C. No. 1 Lake Park',
                    province: 'America',
                    city: 'Washington, D.C.',
                    zip: 100000
                },
                {
                    name: 'Joe Black',
                    age: 30,
                    address: 'Sydney No. 1 Lake Park',
                    province: 'Australian',
                    city: 'Sydney',
                    zip: 100000
                },
                {
                    name: 'Jon Snow',
                    age: 26,
                    address: 'Ottawa No. 2 Lake Park',
                    province: 'Canada',
                    city: 'Ottawa',
                    zip: 100000
                },
                {
                    name: 'John Brown',
                    age: 18,
                    address: 'New York No. 1 Lake Park',
                    province: 'America',
                    city: 'New York',
                    zip: 100000
                },
                {
                    name: 'Jim Green',
                    age: 24,
                    address: 'Washington, D.C. No. 1 Lake Park',
                    province: 'America',
                    city: 'Washington, D.C.',
                    zip: 100000
                },
                {
                    name: 'Joe Black',
                    age: 30,
                    address: 'Sydney No. 1 Lake Park',
                    province: 'Australian',
                    city: 'Sydney',
                    zip: 100000
                },
                {
                    name: 'Jon Snow',
                    age: 26,
                    address: 'Ottawa No. 2 Lake Park',
                    province: 'Canada',
                    city: 'Ottawa',
                    zip: 100000
                }
            ],

            height: 600,
            currentPage: 1,
            totalItems: 10,
            pageSize: 10,
        }
    },
    created() {
        this.initHeight()
    },
    mounted() {
        // this.getPageData()
    },
    methods: {
        initHeight() {
            this.$nextTick(() => {
                this.height = this.$refs.tableRef.offsetHeight - 120;
            })

        },
        async getPageData() {
            let param = {
                page: this.currentPage,
                rows: this.pageSize
            }
            docLogRequest.getDocLogList(param).then(res => {
                console.log(res)
                if (res.code === 200) {
                    let result = res.data.data;
                    this.totalItems = res.data.total;
                    this.data = []
                    let obj = {}

                    for (let resultElement of result) {
                        obj['time'] = parseTime(new Date(), '{y}年{m}月{d}日 {h}:{i}:{s}');// resultElement['createTime']
                        obj['user'] = resultElement['user']
                        obj['action'] = resultElement['action']
                        obj['name'] = resultElement['docName']

                        this.data.push(obj)
                        obj = {}
                    }
                }
            }).catch(err => {
                console.log(err)
            })
            const result = [{
                id: "id",
                createTime: new Date(),
                user: "sdjfsljfdsjl",
                action: "下载",
                docName: "文档名称脸上的肌肤来说减肥路上"
            }, {
                id: "id",
                createTime: new Date(),
                user: "sdjfsljfdsjl",
                action: "下载",
                docName: "文档名称脸上的肌肤来说减肥路上"
            }, {
                id: "id",
                createTime: new Date(),
                user: "sdjfsljfdsjl",
                action: "下载",
                docName: "文档名称脸上的肌肤来说减肥路上"
            }, {
                id: "id",
                createTime: new Date(),
                user: "sdjfsljfdsjl",
                action: "下载",
                docName: "文档名称脸上的肌肤来说减肥路上"
            }]


        },

        remove(item) {
            this.$Message.info('receive cancel');
        },

        removeBatch() {
            let selection = this.$refs.logTable.getSelection();
            console.log(selection)
        },
        pageChange(page) {
            this.currentPage = page
            this.getPageData()
        },
    }
}
</script>

<style scoped lang="scss">

.main {
    height: 100%;
    width: 100%;

    position: relative;

    .bottom-zone {
        position: absolute;

        bottom: 50px;
        left: 0;

        width: 100%;
        height: 80px;
        line-height: 80px;

        .bottom-zone-left {

        }

        .bottom-zone-right {
            text-align: right;
        }
    }

}
</style>
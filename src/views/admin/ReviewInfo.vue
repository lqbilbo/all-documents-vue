<template>
    <div class="main" ref="tableRef">

        <Table ref="reviewInfoTable" width="100%" :height="height" border :columns="columns" :data="data">
            <template #checkSate="{row ,index}">
                <Tag v-if="row.checkState === true" color="success">通过</Tag>
                <Tag v-else-if="row.checkState === false" color="error">拒绝</Tag>
                <Tag v-else color="primary">未知</Tag>
            </template>
            <template #readState="{row ,index}">
                <Tag v-if="row.readState === true" type="border" color="success">已读</Tag>
                <Tag v-else type="border" color="primary">未读</Tag>
            </template>
            <template #action="{ row, index }">
                <Button type="error" size="small" @click="remove(index)">清  除</Button>
            </template>


        </Table>

        <div class="bottom-zone">
            <Row>
                <Col span="12" class="bottom-zone-left">
                    <Button type="primary" ghost @click="removeBatch">全部删除</Button>
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

import {parseTime} from "@/utils"

import reviewRequest from '@/api/docReview'

export default {
    name: "ReviewInfo",
    data () {
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
                    width: 200
                },
                {
                    title: '文档名称',
                    key: 'name',
                    minWidth: 200,
                    maxWidth: 330
                    // fixed: 'left'
                },
                {
                    title: '提交用户',
                    key: 'user',
                    width: 200
                },
                {
                    title: '审核状态',
                    // key: 'checkState',
                    slot: 'checkSate',
                    width: 120
                },
                {
                    title: '理由',
                    key: 'viewInfo',
                    // width: 300
                },
                {
                    // 已读/未读
                    title: '状态',
                    slot: 'readState',
                    width: 120
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
        // this.getDocData()
    },
    methods: {
        initHeight() {
            this.$nextTick(() => {
                this.height = this.$refs.tableRef.offsetHeight - 120;
            })
        },
        async getDocData() {
            let param = {
                page: this.currentPage,
                rows: this.pageSize
            }
            reviewRequest.getReviewLog(param).then(res => {
                if (res.code === 200 ){
                    let result = res.data.data;
                    this.data = []
                    let obj = {}
                    for (let resultElement of result) {
                        obj['id'] = resultElement['id']
                        obj['name'] = resultElement['docName']

                        obj['user'] = resultElement['createUser'] || '未知'

                        obj['time'] = parseTime(new Date(), '{y}年{m}月{d}日 {h}:{i}:{s}'); //resultElement['createTime']

                        obj['checkState'] = resultElement['checkState']

                        let viewInfo = resultElement['reviewLog']
                        obj['viewInfo'] = viewInfo
                        obj['readState'] = resultElement['readState']
                        this.data.push(obj)
                        obj = {}
                    }
                }
            }).catch(err => {
                console.log(err)
            })

        },

        async remove(index) {
            this.$Message.info('remove cancel');

            let item = this.data[index]
            let param = {
                ids: [item.id]
            }
            reviewRequest.removeReviewLog(param).then(res => {
                if (res.code === 200) {
                    this.getDocData()
                }

            }).catch(err => {
                console.log(err)
            })

        },

        removeBatch() {
            let selection = this.$refs.reviewInfoTable.getSelection();
            console.log(selection)
        },

        pageChange(page) {
            this.currentPage = page
            this.getDocData()
        },



    }
}
</script>

<style scoped lang="scss">

.main {
    width: 100%;
    height: 100%;
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
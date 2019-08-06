/**
*   男女比例饼图（中间有总数，比例图有显示数据）
 */
<template>
    <div id="VnameQueryPie">
        <div class="pie-title">
            <span class="pie-line"></span>
            <span class="title-span">男女比例&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;单位:人</span>
        </div>
        <div id="nameQueryPieArea" class="pie-area">

        </div>
    </div>
</template>
<script>
export default {
    name: 'VnameQueryPie',
    props: {

    },
    data () {
        return {
            option: {
                title: {
                    // text: '某站点用户访问来源',
                    // subtext: '纯属虚构',
                    // x: 'center'
                },
                tooltip: {
                    trigger: 'item',
                    formatter: "{a} <br/>{b} : {c} ({d}%)"
                },
                color: ['#72C4FE', '#F15351'],
                legend: {
                    orient: 'vertical',
                    left: '20px',
                    data: ['男', '女'],
                    selected: {
                        '男': true,
                        '女': true
                    }
                },
                graphic: { // 图形中间文字
                    type: "text",
                    left: "center",
                    top: "center",
                    style: {
                        text: '总数\n\n8800',
                        textAlign: "center",
                        fill: "#333",
                        fontSize: 14
                    }
                },
                series: [
                    {
                        name: '访问来源',
                        type: 'pie',
                        radius: ['30%', '60%'],
                        center: ['50%', '50%'],
                        data: [
                            {value: 3147, name: '女'},
                            {value: 5663, name: '男'}
                        ],
                        label: {
                            normal: {
                                formatter: '{c} 人'
                            }
                        }
                    }
                ]
            }
        };
    },
    methods: {
        drawPie () {
            let myPie = this.$echarts.init(document.getElementById('nameQueryPieArea'));
            myPie.setOption(this.option);
        }
    },
    created () {
    },
    mounted () {
        this.drawPie();
    }
};
</script>
<style lang="scss" scope>
#VnameQueryPie .pie-title{
    font-size: 14px;
    margin-top: 12px;
    margin-left: 12px;
    .pie-line{
        width:2px;
        height:12px;
        display: -webkit-inline-box;
        background:rgba(21,162,225,1);
    }
}
#VnameQueryPie .pie-area{
    height: 454px;
    width: 532px;
    margin-top: 12px;
}
</style>
 

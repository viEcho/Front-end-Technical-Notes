/**
* 生肖 分布图--手写圆环分布
 */

 <template>
    <div id="VnameQueryShengxiao">
        <div class="pie-title">
            <span class="pie-line"></span>
            <span class="title-span">生肖分布&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;单位:人</span>
        </div>
        <div id="shengxiaoArea" class="container" ref="container">
            <div class="dot" ref="dot"></div>
            <div v-for="(item, index) in shengxiaoDatas" class="text" :class="'shengxiaoText'+index" :key="index" ref="box">
                    {{item.value}}人
            </div>
            <div v-for="(item, index) in shengxiaoDatas" class="box" :class="'shengxiao'+index" :key="index" ref="box">
                    {{item.shengxiao}}
            </div>
        </div>
    </div>
</template>
<script>
export default {
    name: 'VnameQueryShengxiao',
    props: {
    },
    data () {
        return {
            shengxiaoDatas: [
                {shengxiao: '龙', color: '#96C8E3', value: '500'},
                {shengxiao: '蛇', color: '#4FDAD2', value: '500'},
                {shengxiao: '马', color: '#72C4FE', value: '500'},
                {shengxiao: '羊', color: '#3EAFF7', value: '500'},
                {shengxiao: '猴', color: '#605AD8', value: '500'},
                {shengxiao: '鸡', color: '#BA51F1', value: '500'},
                {shengxiao: '狗', color: '#F151BE', value: '500'},
                {shengxiao: '猪', color: '#BA1816', value: '500'},
                {shengxiao: '鼠', color: '#F15351', value: '500'},
                {shengxiao: '牛', color: '#F3D38C', value: '500'},
                {shengxiao: '虎', color: '#FEBF0E', value: '500'},
                {shengxiao: '兔', color: '#96E3A4', value: '500'}
            ]
        };
    },
    methods: {
        drewPie () {
            // 中心点坐标
            var dotLeft = window.getComputedStyle(this.$refs.dot).left;
            var dotTop = window.getComputedStyle(this.$refs.dot).top;
            dotLeft = parseInt(dotLeft.substring(0, dotLeft.indexOf('px')));
            dotTop = parseInt(dotTop.substring(0, dotTop.indexOf('px')));
            // 半径
            var outerRadius = 150;
            var innerRadius = 100;
            // 每一个BOX对应的角度;
            var avd = 360 / 12;
            // 每一个BOX对应的弧度;
            var ahd = avd * Math.PI / 180;
            // 设置圆的中心点的位置
            this.$refs.dot.style.left = dotLeft + 'px';
            this.$refs.dot.style.top = dotTop + 'px';
            this.$nextTick(() => {
                this.shengxiaoDatas.forEach((item, index) => {
                    document.querySelector('.shengxiao' + index).style.left = Math.sin((ahd * index)) * outerRadius + dotLeft + 'px';
                    document.querySelector('.shengxiao' + index).style.top = Math.cos((ahd * index)) * outerRadius + dotTop + 'px';
                    document.querySelector('.shengxiao' + index).style.background = item.color;
                    document.querySelector('.shengxiaoText' + index).style.left = Math.sin((ahd * index)) * innerRadius + dotLeft + 10 + 'px';
                    document.querySelector('.shengxiaoText' + index).style.top = Math.cos((ahd * index)) * innerRadius + dotTop + 18 + 'px';
                });
            });
        }
    },
    created () {
        
    },
    mounted () {
        this.drewPie();
    }
};
</script>
<style lang="scss" scope>
#VnameQueryShengxiao{
    width:100%;
    height: 100%;
    display: block;
    position: relative;
}
#VnameQueryShengxiao .pie-title{
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
#VnameQueryShengxiao .container{
    height: 454px;
    min-width: 532px;
    margin-top: 12px;
    display: block;
    .dot{
        position: relative;
        width: 50px;
        height: 50px;
        line-height: 50px;
        text-align: center;
        border-radius: 25px;
        background-color: #fff;
        left: 50%;
        top: 50%;
    }
    .text{
        width: 50px;
        height: 50px;
        position: absolute;
    }
    .box{
        width: 50px;
        height: 50px;
        color: #fff;
        text-align: center;
        line-height: 50px;
        border-radius: 25px;
        position: absolute;
    }
}
</style>
 

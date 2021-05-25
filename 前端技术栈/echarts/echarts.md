

# 饼图

```typescript
import * as echarts from 'echarts';
 private chart: echarts.ECharts|null = null;

  projectEchartInit(){
    let chartBox = document.getElementById('projectChart');
    if(!chartBox) {
      this.showWarnInfo('科创部立项图加载失败');
      return;
    }
    
    this.chart = echarts.init((chartBox))
    this.chart.setOption({
      tooltip: {
         //鼠标移入时的项目信息提示
        trigger: 'item'
      },
      series: [
        {
          name: '访问来源',
          type: 'pie',
          radius: '50%',
          data: [
            {value: this.local, name: '科创部立项'},
            {value: this.notLocal, name: '非科创部立项'},
          ],
          emphasis: {
             //鼠标移入时的项目范围、图形大小提示，范围阴影设置
            itemStyle: {
              shadowBlur: 10,
              shadowOffsetX: 0,
              shadowColor: 'rgba(0, 0, 0, 0.5)'
            }
          }
        }
      ]
    });
  }

mounted() {
    this.projectEchartInit();
  }
```

目前，写死的
思路：
从接口中获取数据
然后
init中接将接口数据循环

# 折线图

```typescript

```


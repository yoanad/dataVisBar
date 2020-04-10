<template>
  
  <div id="wrap"  >
    <div id="model" class="flat-select" @change="changeData" ></div>
    <div id="barItem" >
      <apexchart
        type="bar"
        width="500"
        height="420"
        ref="chart"
        :options="chartOptions"
        :series="series"
      ></apexchart>
    </div>
    <div id="chart-quarter">
      <apexchart
        type="bar"
        width="500"
        height="420"
        ref="chartQuarter"
        :options="chartOptionsQuarter"
        :series="seriesQuarter"
      ></apexchart>
    </div>
  </div>
</template>

<script>
// import VueApexCharts from './components/VueApexCharts';
import VueApexCharts from "vue-apexcharts";

Apex = {
  chart: {
    toolbar: {
      show: true
    }
  },
  tooltip: {
    shared: false
  },
  legend: {
    show: false
  }
};

var colors = [
  "#008FFB",
  "#00E396",
  "#FEB019",
  "#FF4560",
  "#775DD0",
  "#00D9E9",
  "#FF66C3"
];

/**
 * Randomize array element order in-place.
 * Using Durstenfeld shuffle algorithm.
 */
function shuffleArray(array) {
  // for (var i = array.length - 1; i > 0; i--) {
  //   var j = Math.floor(Math.random() * (i + 1));
  //   var temp = array[i];
  //   array[i] = array[j];
  //   array[j] = temp;
  // }
  return array;
}

var arrayData = [
  {
    //t-shirt
    y: 2230,
    quarters: [
      {
        x: "China",
        y: 1009
      },
      {
        x: "USA",
        y: 1125
      },
      {
        x: "India",
        y: 4331
      },
      {
        x: "Pakistan",
        y: 2457
      }
    ]
  },
  {
    //skirt
    y: 4014,
    quarters: [
      {
        x: "China",
        y: 1816
      },
      {
        x: "USA",
        y: 2024
      },
      {
        x: "India",
        y: 7796
      },
      {
        x: "Pakistan",
        y: 4422
      }
    ]
  },
  {
    //jeans
    y: 6692,
    quarters: [
      {
        x: "China",
        y: 3027
      },
      {
        x: "USA",
        y: 3373
      },
      {
        x: "India",
        y: 12993
      },
      {
        x: "Pakistan",
        y: 7371
      }
    ]
  },
  {
    //dress
    y: 5799,
    quarters: [
      {
        x: "China",
        y: 2623
      },
      {
        x: "USA",
        y: 2924
      },
      {
        x: "India",
        y: 11261
      },
      {
        x: "Pakistan",
        y: 6388
      }
    ]
  },
  {
    y: 540,
    quarters: [
      {
        x: "China",
        y: 120
      },
      {
        x: "USA",
        y: 120
      },
      {
        x: "India",
        y: 130
      },
      {
        x: "Pakistan",
        y: 170
      }
    ]
  },
  {
    y: 580,
    quarters: [
      {
        x: "China",
        y: 170
      },
      {
        x: "USA",
        y: 130
      },
      {
        x: "India",
        y: 120
      },
      {
        x: "Pakistan",
        y: 160
      }
    ]
  }
];

function makeData() {
  var dataSet = shuffleArray(arrayData);

  var dataYearSeries = [
    {
      x: "T-shirts",
      y: dataSet[0].y,
      color: colors[0],
      quarters: dataSet[0].quarters
    },
    {
      x: "Skirt",
      y: dataSet[1].y,
      color: colors[1],
      quarters: dataSet[1].quarters
    },
    {
      x: "Jeans",
      y: dataSet[2].y,
      color: colors[2],
      quarters: dataSet[2].quarters
    },
    {
      x: "Dress",
      y: dataSet[3].y,
      color: colors[3],
      quarters: dataSet[3].quarters
    }
  ];

  return dataYearSeries;
}

function updateQuarterChart(sourceChart, destChartIDToUpdate) {
  var series = [];
  var seriesIndex = 0;
  var colors = [];

  if (sourceChart.w.globals.selectedDataPoints[0]) {
    var selectedPoints = sourceChart.w.globals.selectedDataPoints;
    for (var i = 0; i < selectedPoints[seriesIndex].length; i++) {
      var selectedIndex = selectedPoints[seriesIndex][i];
      var yearSeries = sourceChart.w.config.series[seriesIndex];
      series.push({
        name: yearSeries.data[selectedIndex].x,
        data: yearSeries.data[selectedIndex].quarters
      });
      colors.push(yearSeries.data[selectedIndex].color);
    }

    if (series.length === 0)
      series = [
        {
          data: []
        }
      ];

    return ApexCharts.exec(destChartIDToUpdate, "updateOptions", {
      series: series,
      colors: colors,
      fill: {
        colors: colors
      }
    });
  }
}

export default {
  components: {
    apexchart: VueApexCharts
  },
  name: "app",
  data: function() {
    return {
      series: [
        {
          data: makeData()
        }
      ],
      chartOptions: {
        chart: {
          id: "barItem",
          height: 300,
          width: "100%",
          type: "bar",
          events: {
            dataPointSelection: function(e, chart, opts) {
              var quarterChartEl = document.querySelector("#chart-quarter");
              var yearChartEl = document.querySelector("#chart-year");

              if (opts.selectedDataPoints[0].length === 1) {
                if (quarterChartEl.classList.contains("active")) {
                  updateQuarterChart(chart, "barQuarter");
                } else {
                  yearChartEl.classList.add("chart-quarter-activated");
                  quarterChartEl.classList.add("active");
                  updateQuarterChart(chart, "barQuarter");
                }
              } else {
                updateQuarterChart(chart, "barQuarter");
              }

              if (opts.selectedDataPoints[0].length === 0) {
                yearChartEl.classList.remove("chart-quarter-activated");
                quarterChartEl.classList.remove("active");
              }
            },
            updated: function(chart) {
              updateQuarterChart(chart, "barQuarter");
            }
          }
        },
        plotOptions: {
          bar: {
            distributed: true,
            horizontal: true,
            barHeight: "60%",
            dataLabels: {
              position: "bottom"
            }
          }
        },
        dataLabels: {
          enabled: true,
          textAnchor: "start",
          style: {
            colors: ["#fff"]
          },
          formatter: function(val, opt) {
            return opt.w.globals.labels[opt.dataPointIndex];
          },
          offsetX: 0,
          dropShadow: {
            enabled: true
          }
        },

        colors: colors,

        states: {
          normal: {
            filter: {
              type: "desaturate"
            }
          },
          active: {
            allowMultipleDataPointsSelection: true,
            filter: {
              type: "darken",
              value: 1
            }
          }
        },
        tooltip: {
          x: {
            show: false
          },
          y: {
            title: {
              formatter: function(val, opts) {
                return opts.w.globals.labels[opts.dataPointIndex];
              }
            }
          }
        },
        title: {
          text: "Your Clothing choice",
          offsetX: 15,
          margin: 10,
          style: {
            fontSize: 28
          }
        },
        subtitle: {
          text: "(Click on bar to see details)",
          //margin: 30,
          offsetX: 15,
          offsetY: 50,
          style: {
            fontSize: 18
          }
        },
        yaxis: {
          labels: {
            show: false
          }
        },
        xaxis: {
          title: {
            text: "world average",
            style: {
              fontSize: "14px",
              fontFamily: "Helvetica, Arial, sans-serif",
              fontWeight: 600,
              cssClass: "apexcharts-yaxis-title"
            }
          }
        }
      },

      seriesQuarter: [
        {
          data: []
        }
      ],
      chartOptionsQuarter: {
        chart: {
          id: "barQuarter",
          height: 300,
          width: "100%",
          type: "bar",
          stacked: false
        },
        plotOptions: {
          bar: {
            columnWidth: "80%",
            horizontal: false
          }
        },
        legend: {
          show: true
        },
        grid: {
          yaxis: {
            lines: {
              show: false
            }
          },
          xaxis: {
            lines: {
              show: true
            }
          }
        },
        yaxis: {
          labels: {
            show: false
          }
        },
        title: {
          text: "Country by country analysing",
          offsetX: 10,
          style: {
            fontSize: 20
          }
        },
        tooltip: {
          x: {
            formatter: function(val, opts) {
              return opts.w.globals.seriesNames[opts.seriesIndex];
            }
          },
          y: {
            title: {
              formatter: function(val, opts) {
                return opts.w.globals.labels[opts.dataPointIndex];
              }
            }
          }
        }
      },

      methods: {
        changeData: function() {
          this.$refs.chart.updateSeries([
            {
              data: makeData()
            }
          ]);
        }
      }
    };
  }
};
</script>

<style scoped>
      
        body {
      background: #fff;
    }
    
    #wrap {
      margin: 45px auto;
      max-width: 800px;
      position: relative;
    }
    
    .chart-box {
      padding-left: 0;
    }

        select#model {
      display: none;
      position: absolute;
      top: -40px;
      left: 0;
      z-index: 2;
      cursor: pointer;
      transform: scale(0.8);
    }
</style>


<template>
  <section id="experience">
    <AnimateOnVisible name="fadeDown" :duration="1">
      <Title
        class="title"
        :title="content.title"
        :description="content.description"
      />
    </AnimateOnVisible>
    <div ref="timelineChart"></div>
    <!-- <AnimateOnVisible name="fadeUp" :duration="1" @visibilityChanged="handleVisibilityChanged">
      <div class="container-fluid">
        <div class="row">
          <ExperienceColumn
            :posts="content.academic"
            title="Education"
            class="col-12 col-md left"
          />
          <ExperienceColumn
            :posts="content.professional"
            title="Professional"
            class="col-12 col-md right"
          />
        </div>
      </div>
    </AnimateOnVisible> -->
  </section>
</template>

<script>
import Title from "./Title.vue";
// import ExperienceColumn from "./ExperienceColumn.vue";
import * as joint from 'jointjs'

export default {
  name: "Experience",
  props: ["content"],
  components: {
    Title,
    // ExperienceColumn
  },
  data() {
    return {
      isTimelineChartReady: false,
      paper: null
    };
  },
  methods: {
    displayTimelineChart() {
      const timelineChart = this.$refs.timelineChart;

      const { dia, util, shapes: defaultShapes } = joint;
      const chevronCount = 40;
      const chevronHeight = 30;
      const chevronWidth = 8;
      const timelineColor = '#fff';
      const backgroundColor = '#444';
      const padding = -100;
      const xPadding = 160;
      const gap = 2500 / 7;
      const timelineY = 600;
      const timelineXMin = 200;
      const timelineXMax = 3000;

      const timelineEventJSONMarkup = util.svg`
          <ellipse @selector="body"/>
          <text @selector="title"/>
          <text @selector="subtitle"/>
          <text @selector="description"/>
      `;

      class TimelineEvent extends dia.Element {

          defaults() {
              return {
                  ...super.defaults,
                  type: 'TimelineEvent',
                  attrs: {
                      root: {
                          magnetSelector: 'body'
                      },
                      body: {
                          stroke: 'none',
                          cx: 'calc(w/2)',
                          cy: 'calc(h/2)',
                          rx: 'calc(w/2)',
                          ry: 'calc(h/2)',
                      },
                      title: {
                          text: 'Label Inside',
                          fontSize: 42,
                          fontFamily: 'sans-serif',
                          fill: timelineColor,
                          textVerticalAnchor: 'top',
                          textAnchor: 'end',
                          x: 'calc(w + 20)',
                          y: 'calc(h + 10)'
                      },
                      subtitle: {
                          text: 'Subtitle',
                          fontSize: 30,
                          fontFamily: 'sans-serif',
                          fontWeight: 'bold',
                          fill: timelineColor,
                          textVerticalAnchor: 'top',
                          textAnchor: 'end',
                          x: 'calc(w)',
                          y: 'calc(h + 50)'
                      },
                      description: {
                          text: 'Description',
                          fontSize: 24,
                          fontFamily: 'sans-serif',
                          fill: timelineColor,
                          textVerticalAnchor: 'top',
                          textAnchor: 'end',
                          x: 'calc(w)',
                          y: 'calc(h + 60)'
                      },

                  }
              };
          }

          preinitialize() {
              this.markup = timelineEventJSONMarkup;
          }

          positionLabels() {
            const bbox = this.getBBox() - 1000; // Get bounding box of the element
            const centerX = bbox.x + bbox.width / 2;

            this.attr({
                title: {
                    x: centerX,
                    textAnchor: 'middle' // Center align text
                },
                subtitle: {
                    x: centerX,
                    textAnchor: 'middle' // Center align text
                },
                description: {
                    x: centerX,
                    textAnchor: 'middle' // Center align text
                }
            });
              if (this.position().y > timelineY) {
                  // if (this.attr('title/y') === 'calc(h + 10)') return;
                  this.attr({
                      title: {
                          y: 'calc(h + 10)',
                          textVerticalAnchor: 'top'
                      },
                      subtitle: {
                          y: 'calc(h + 60)',
                          textVerticalAnchor: 'top'
                      },
                      description: {
                          y: 'calc(h + 60)',
                          textVerticalAnchor: 'top'
                      }
                  });
              } else {
                  // if (this.attr('title/y') === -10) return;
                  this.attr({
                      title: {
                          y: -10,
                          textVerticalAnchor: 'bottom'
                      },
                      subtitle: {
                          y: -60,
                          textVerticalAnchor: 'bottom'
                      },
                      description: {
                          y: -60,
                          textVerticalAnchor: 'bottom'
                      }
                  });
              }
          }
      }

      const shapes = { ...defaultShapes, TimelineEvent };

      const graph = new dia.Graph({}, {
          cellNamespace: shapes
      });

      const paper = new dia.Paper({  
          width: "100%",
          height: "500",
          model: graph,
          defaultConnectionPoint: {
              name: 'boundary'
          },
          cellViewNamespace: shapes,
          interactive: (cellView) => {
              return (cellView.model instanceof TimelineEvent);
          },
          restrictTranslate(elementView) {
              const timelineMargin = 20;
              const bbox = elementView.model.getBBox();
              const xMin = timelineXMin;
              const xMax = timelineXMax - bbox.width;
              const yMin = timelineY - bbox.height - timelineMargin;
              const yMax = timelineY + timelineMargin;
              return function(x, y) {
                  return {
                      x: Math.max(xMin, Math.min(xMax, x)),
                      y: (y > timelineY) ? Math.max(yMax, y) : Math.min(yMin, y)
                  };
              };
          },
          gridSize: 10,
          async: true,
          sorting: dia.Paper.sorting.APPROX,
          defaultLink: () => new shapes.standard.DoubleLink(),
          defaultLinkAnchor: { name: 'connectionPerpendicular' }
      });

      this.paper = paper.el;
      timelineChart.appendChild(paper.el);
      // Timeline

      const start = new shapes.standard.Ellipse({
          position: {
              x: timelineXMin - 150,
              y: timelineY - 90
          },
          size: {
              width: 180,
              height: 180
          },
          attrs: {
              root: {
                  pointerEvents: 'none'
              },
              body: {
                  stroke: timelineColor,
                  fill: backgroundColor,
                  strokeWidth: 3
              },
              label: {
                  fill: timelineColor,
                  fontFamily: 'sans-serif',
                  fontSize: 40,
                  text: 'Born'
              }
          }
      });
      const end = new shapes.standard.Ellipse({
          position: {
              x: timelineXMax,
              y: timelineY - 90
          },
          size: {
              width: 180,
              height: 180
          },
          attrs: {
              root: {
                  pointerEvents: 'none'
              },
              body: {
                  stroke: timelineColor,
                  fill: backgroundColor,
                  strokeWidth: 3
              },
              label: {
                  fontSize: 40,
                  fill: timelineColor,
                  fontFamily: 'sans-serif',
                  text: 'Present'
              }
          }
      });

      const timeline = new shapes.standard.Link({
          source: {
              id: start.id
          },
          target: {
              id: end.id
          },
          z: -2,
          attrs: {
              root: {
                  pointerEvents: 'none'
              },
              line: {
                  strokeWidth: chevronHeight,
                  stroke: timelineColor,
                  targetMarker: null,
                  vertexMarker: {
                      d: `M -${2 * chevronWidth} -${chevronHeight / 2} h ${chevronWidth} L 0 0 -${chevronWidth} ${chevronHeight / 2} h -${chevronWidth} L -${chevronWidth} 0 z`,
                      fill: backgroundColor,
                      stroke: 'none'
                  }
              }
          },
          vertices: Array.from({ length: chevronCount }).map((_, i) => {
              return {
                  x: timelineXMin + padding + i * (timelineXMax - timelineXMin - padding) / chevronCount,
                  y: timelineY
              };
          })
      });

      graph.addCells([start, end, timeline]);

      graph.on('change:position', function(cell) {
          if (cell instanceof TimelineEvent) {
              cell.positionLabels();
          }
      });

      const colors = ['#F4F269','#E7ED6A','#EBF78A','#CEE26B','#C1DD6B','#B5D76C','#A8D26D','#9BCD6D','#8FC76E','#82C26E','#75BD6F','#89C78F','#5CB270', '#4DA562'];      

      for (let i = 0; i < 8; i++) {
        addEvent(gap * i, gap * i, 0, {
            size: 30,
            color: colors[6],
            title: (2019 + i).toString(),
            // subtitle: 'Version 1.0',
            // description: '● Larger companies start adopting Node.js: LinkedIn, Uber, etc.\n● Hapi is born'
        });
      }

      for (let i in this.content.professional) {
        let duration = this.content.professional[i].duration.split(' - ');
        let startTime = duration[0].split('.'), endTime = duration[1].split('.');
        let x1 = parseInt(startTime[0]) - 2019 + parseInt(startTime[1]) / 12, 
            x2 = parseInt(endTime[0]) - 2019 + parseInt(endTime[1]) / 12;
        addEvent(gap * x1, gap * x2, 130 * this.content.professional[i].layer, {
            size: 0,
            color: colors[2],
            title: this.content.professional[i].title,
            subtitle: this.content.professional[i].subtitle,
            // description: this.content.professional[i].description,
        });
      }

      for (let i in this.content.academic) {
        let duration = this.content.academic[i].duration.split(' - ');
        let startTime = duration[0].split('.'), endTime = duration[1].split('.');
        let x1 = parseInt(startTime[0]) - 2019 + parseInt(startTime[1]) / 12, 
            x2 = parseInt(endTime[0]) - 2019 + parseInt(endTime[1]) / 12;
        addEvent(gap * x1, gap * x2, 130 * this.content.academic[i].layer, {
            size: 0,
            color: colors[11],
            title: this.content.academic[i].title,
            subtitle: this.content.academic[i].subtitle,
            // description: this.content.academic[i].description,
        });
      }

      // Functions

      function shadeHexColor(color, percent) {
          const f = parseInt(color.slice(1), 16);
          const t = percent < 0 ? 0 : 255;
          const p = percent < 0 ? percent * -1 : percent;
          const R = f>>16;
          const G = f>>8&0x00FF;
          const B = f&0x0000FF;
          return '#' + (0x1000000+(Math.round((t-R)*p)+R)*0x10000 + (Math.round((t-G)*p)+G)*0x100 + (Math.round((t-B)*p)+B)).toString(16).slice(1);
      }

      function addEvent(x1, x2, y, options = {}) {
        const { color = '#000', title = '', subtitle = '', description = '', size = 30 } = options;
        const midX = (x1 + x2) / 2;
        
        const event = new TimelineEvent({
            position: {
                x: timelineXMin + xPadding - size / 2 + midX,
                y: timelineY + y - size / 2,
            },
            size: {
                width: size,
                height: size
            },
            attrs: {
                body: {
                    fill: color,
                },
                title: {
                    fill: color,
                    text: title
                },
                subtitle: {
                    text: subtitle,
                    fill: shadeHexColor(color, 0.5)
                },
                description: {
                    text: description,
                    textWrap: { width: 120, height: null }
                }
            }
        });

        // Lines connecting to the timeline
        const line1 = new shapes.standard.Link({
            source: { id: event.id },
            target: { id: timeline.id },
            z: -1,
            vertices: [{ x: timelineXMin + xPadding + x1, y: timelineY + y}],
            attrs: {
                line: {
                    strokeWidth: 3,
                    stroke: timelineColor,
                    strokeDasharray: '2,2',
                    targetMarker: null
                }
            }
        });

        const line2 = new shapes.standard.Link({
            source: { id: event.id },
            target: { id: timeline.id },
            z: -1,
            vertices: [{ x: timelineXMin + xPadding + x2, y: timelineY + y }],
            attrs: {
                line: {
                    strokeWidth: 3,
                    stroke: timelineColor,
                    strokeDasharray: '2,2',
                    targetMarker: null
                }
            }
        });

        event.positionLabels();
        graph.addCells([event, line1, line2]);
    }


      function scaleToFit() {
        paper.scaleContentToFit({
          useModelGeometry: true,
          padding: { horizontal: 20, vertical: 40 }
        });
        const sy = paper.scale().sy;
        paper.translate(0, (paper.getArea().height / 2 - timelineY) * sy);
      }

      window.addEventListener('resize', () => scaleToFit());
      scaleToFit();
    },
    destroyTimelineChart() {
      const timelineChart = this.$refs.timelineChart;
      timelineChart.removeChild(this.paper);
    },
    handleVisibilityChanged(isVisible) {
      this.isTimelineChartReady = isVisible;
      if (isVisible) {
        this.displayTimelineChart();
      }
      else {
        this.destroyTimelineChart();
      }
    }
  },
  mounted() {
    this.displayTimelineChart();
  },
};
</script>

<style scoped lang="scss">
@import "@/styles/constants.scss";

$linear: map-get($colors, dark);

#experience {
  background-color: lighten(map-get($colors, primary), 5%);
}

.title {
  color: map-get($colors, light);
}

.row {
  padding-top: 20px;
  text-align: center;
}

@media (min-width: #{map-get($breakpoints, small)}) {
  .left {
    text-align: right;
    border-right: 2px solid $linear;
  }
  .right {
    text-align: left;
  }
}

@media (max-width: #{map-get($breakpoints, small)}) {
  .right {
    margin-top: 20px;
  }
  .left:before {
    content : "";
    position: absolute;
    left    : 20%;
    bottom  : 0;
    height  : 2px;
    width   : 60%;  /* or 100px */
    border-bottom:2px solid $linear;
  }
}

::v-deep .text-wrapper {
  &:after {
    border-bottom: 1px solid map-get($colors, dark);
  }
}
</style>

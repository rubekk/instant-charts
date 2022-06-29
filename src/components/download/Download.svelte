<script type="text/javascript">
    import './../../app.css';
    import './style.css';
    import chartjs from 'chart.js';
    import { onMount } from 'svelte';
    import { downloadData } from './../../lib/store.js';

    let chart=[],
        ctx=[],
        canvas=[],
        downloadElem=[],
        displayTooltip=[],
        data=[],
        openDownload=false;

    downloadData.subscribe(value=>{
        data=value
    })

    onMount(async ()=>{
        canvas.forEach((item,i)=>{
            ctx[i] = item.getContext('2d');
            displayTooltip[i]={
                id: 'displayTooltip',
                afterDraw(chart, args, options){
                    const { ctx }=chart;
                    ctx.save();

                    chart.data.datasets.forEach((dataset,i)=>{
                        chart.getDatasetMeta(0).data.forEach((dataPoint,index)=>{
                            const { x,y } = dataPoint.tooltipPosition();
                            const text = Array.from(dataset.data)[index];
                            const textWidth = ctx.measureText(text).width;
                            const textHeight = 20;


                            ctx.fillStyle='rgba(0,0,0,0.8)';
                            ctx.fillRect(x-((textWidth/2)+5),(y-(textHeight/2)),textWidth+10,textHeight);

                            ctx.font = '12px Arial';
                            ctx.fillStyle = 'white';
                            ctx.fillText(text,x-textWidth/2,y+5);
                            ctx.restore();
                        })
                    })
                }
            }
            handleChart(i);
        })
    });

    const handleChart=i=>{
        if(chart[i]) chart[i].destroy();

        if(data[i].chartType=='pie' || data[i].chartType=='doughnut'){
            chart[i]=new chartjs(ctx[i], {
                type: data[i].chartType,
                data: {
                    labels: data[i].labelsArr,
                    datasets: [{
                        label: 'Revenue',
                        backgroundColor: data[i].colors,
                        borderColor: data[i].borderColor,
                        borderWidth: data[i].borderWidth,
                        data: data[i].valuesArr
                    }]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    title: {
                        display: false
                    },
                    tooltips: {
                        enabled: !data[i].showTooltip
                    },
                    legend: {
                        display: false
                    }
                },
                plugins: data[i].showTooltip?[displayTooltip[i]]:[]
            });
        }
        else if(data[i].chartType=='bar' || data[i].chartType=='horizontalBar'){
            chart[i]=new chartjs(ctx[i], {
                type: data[i].chartType,
                data: {
                    labels: data[i].labelsArr,
                    datasets: [{
                        label: 'Revenue',
                        backgroundColor: data[i].colors,
                        borderColor: data[i].borderColor,
                        borderWidth: data[i].borderWidth,
                        data: data[i].valuesArr
                    }]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    scales: {
                        xAxes: [{
                            ticks: {
                                beginAtZero: true
                            }
                        }],
                        yAxes: [{
                            ticks: {
                                beginAtZero: true
                            }
                        }]
                    },
                    title: {
                        display: false
                    },
                    tooltips: {
                        enabled: !data[i].showTooltip
                    },
                    legend: {
                        display: false
                    }
                },
                plugins: data[i].showTooltip?[displayTooltip[i]]:[]
            });
        }
        else if(data[i].chartType=='line' || data[i].chartType=='radar'){
            data[i].showLegend=false;
            chart[i]=new chartjs(ctx[i], {
                type: data[i].chartType,
                data: {
                    labels: data[i].labelsArr,
                    datasets: [{
                        label: 'Revenue',
                        backgroundColor: data[i].lineRadarBackground,
                        borderColor: data[i].borderColor,
                        borderWidth: data[i].borderWidth,
                        data: data[i].valuesArr
                    }]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    title: {
                        display: false
                    },
                    tooltips: {
                        enabled: !data[i].showTooltip
                    },
                    legend: {
                        display: false
                    }
                },
                plugins: data[i].showTooltip?[displayTooltip[i]]:[]
            });
        }
    }

    const downloadImg=()=>{
         downloadElem.forEach((elem,index)=>{
            html2canvas(downloadElem[index], {
                removeContainer: false,
                onclone: function (doc) {
                    let hiddenDiv = doc.querySelectorAll('.download-elem')[index];
                    hiddenDiv.style.display = 'block';
                }
            }).then(function(canvas) {
                const imgURL = canvas.toDataURL('image/png');
                const anchor = document.createElement('a');

                anchor.setAttribute('href',imgURL);
                anchor.setAttribute('download',`chart.png`);
                anchor.click();
            });
        })
    }

    $:  {
        canvas.forEach((item,i)=>{
            ctx[i] = item.getContext('2d');
            displayTooltip[i]={
                id: 'displayTooltip',
                afterDraw(chart, args, options){
                    const { ctx }=chart;
                    ctx.save();

                    chart.data.datasets.forEach((dataset,i)=>{
                        chart.getDatasetMeta(0).data.forEach((dataPoint,index)=>{
                            const { x,y } = dataPoint.tooltipPosition();
                            const text = Array.from(dataset.data)[index];
                            const textWidth = ctx.measureText(text).width;
                            const textHeight = 20;


                            ctx.fillStyle='rgba(0,0,0,0.8)';
                            ctx.fillRect(x-((textWidth/2)+5),(y-(textHeight/2)),textWidth+10,textHeight);

                            ctx.font = '12px Arial';
                            ctx.fillStyle = 'white';
                            ctx.fillText(text,x-textWidth/2,y+5);
                            ctx.restore();
                        })
                    })
                }
            }
            handleChart(i);
        })
    }
</script>

<svelte:head>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/html2canvas/1.4.1/html2canvas.min.js" integrity="sha512-BNaRQnYJYiPSqHHDb58B0yaPfCu+Wgds8Gp/gU33kqBtgNS4tSPHuGibyoeqMV/TJlSKda6FXzoEyYGjTe+vXA==" crossorigin="anonymous" referrerpolicy="no-referrer"></script>
</svelte:head>

<div class="charts-wrapper">
{#each data as indData,index}
    {#if indData.download}
        <div bind:this={downloadElem[index]} class="download-elem">
            <!-- title -->
            <h1 style={`font-size:${indData.titleSize}px; color:${indData.titleColor}`}>{indData.title}</h1>
            
            <!-- canvas -->
            <div class="canvas-sec">
                <canvas bind:this={canvas[index]}></canvas>
            </div>

            <!-- legend -->
            {#if data[index].showLegend===true}
                {#if data[index].legendStyle=='vertical'}
                    <div class="legend-sec" style={`display: ${data[index].labelsArr.length>=1 ? 'block': 'none'}; background: ${data[index].legendBackground}; color: ${data[index].legendColor}; border: ${data[index].legendBorderWidth}px solid ${data[index].legendBorderColor}; border-radius: ${data[index].legendBorderRadius}px; float: ${data[index].legendPosition=='left'?'left':data[index].legendPosition=='right'?'right':'none'}; margin: ${data[index].legendPosition=='center'? '2rem auto 1rem': '2rem 1rem 1rem'}`}>
                        <ul>
                            {#each data[index].labelsArr as label,i}
                                <li>
                                    <div class="legend-label-color" style={`background-color:${data[index].colors[i]}; margin-right: 1rem`}></div>
                                    <div class="legend-label-text">{label}{data[index].showValues && data[index].valuesArr[i]?` (${data[index].valuesArr[i]})`:''}</div>
                                </li>
                            {/each}
                        </ul>   
                    </div>
                {:else if data[index].legendStyle=='horizontal'}
                     <div class="legend-sec-horizontal" style={`display: ${data[index].labelsArr.length>=1 ? 'block': 'none'}`}>
                        <ul>
                            {#each data[index].labelsArr as label,i}
                                <li>
                                    <div class="legend-label-color" style={`background-color:${data[index].colors[i]}`}></div>
                                    <div class="legend-label-text">{label}{data[index].showValues?` (${data[index].valuesArr[i]})`:''}</div>
                                </li>
                            {/each}
                        </ul>   
                    </div>
                {/if}
            {/if}
        </div>
    {/if}
{/each}
</div>

<div class="download-sec">
    <div class="upper" style={`border-bottom: ${openDownload?'1px solid #000':''}; border-radius: ${openDownload?'3px 3px 0 0':''}`}>
        <p on:click={()=>{openDownload=!openDownload}}>
            <span>{data.length==0?'No charts':data.length==1?data[0].title:`${data.length} charts`}</span>
            <i class={openDownload?'fa-solid fa-chevron-up':'fa-solid fa-chevron-down'}></i>
        </p>
        <div class="download-btn" on:click={downloadImg}>Download</div>
    </div>
    {#if openDownload}
        <div class="lower">
            {#each data as indData,index}
                <div>
                    <input type="checkbox" bind:checked={data[index].download}>
                    <span>{data[index].title}</span>
                </div>
            {/each}
        </div>
    {/if}
</div>
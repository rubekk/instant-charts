<script type="text/javascript">
    import './style.css';
    import chartjs from 'chart.js';
    import { onMount } from 'svelte';
    import { downloadData } from './../../lib/store.js';

    let chart,
        ctx,
        canvas,
        currChartIndex=0,
        currChartText='Doughnut',
        openSelect=false,
        openColors=false,
        openLegends=false,
        openTitle=false,
        openDownload=false,
        downloadAs='',
        download=false,
        data=[
            {
                chartType: 'doughnut',
                title: 'Title 1',
                titleSize: 20,
                titleColor: '#000000',
                labels: '',
                values: '',
                labelsArr: ['Jan','Feb','March','April','June'],
                valuesArr: [1,2,3,4,5],
                colors: [],
                borderColor: '#000000',
                borderWidth: 2,
                lineRadarBackground: '#A1B2C3',
                legendColor: '#000000',
                legendStyle: 'vertical',
                legendBorderWidth: 2,
                legendBorderRadius: 2,
                legendBorderColor: '#dcdcdc',
                legendBackground: '#ABC123',
                legendPosition: 'center',
                showLegend: true,
                showTooltip: true,
                showValues: true,
                download: true
            }
        ];

    onMount(async ()=>{
        ctx=canvas.getContext('2d');
        handleChart();
    });

    const handleChart=()=>{
        if(chart) chart.destroy();

        if(data[currChartIndex].chartType=='pie' || data[currChartIndex].chartType=='doughnut'){
            // data[currChartIndex].showLegend=true;
            chart=new chartjs(ctx, {
                type: data[currChartIndex].chartType,
                data: {
                    labels: data[currChartIndex].labelsArr,
                    datasets: [{
                        label: 'Revenue',
                        backgroundColor: data[currChartIndex].colors,
                        borderColor: data[currChartIndex].borderColor,
                        borderWidth: data[currChartIndex].borderWidth,
                        data: data[currChartIndex].valuesArr
                    }]
                },
                options: {
                    title: {
                        display: false
                    },
                    tooltips: {
                        enabled: !data[currChartIndex].showTooltip
                    },
                    legend: {
                        display: false
                    }
                },
                plugins: data[currChartIndex].showTooltip?[displayTooltip]:[]
            });
        }
        else if(data[currChartIndex].chartType=='bar' || data[currChartIndex].chartType=='horizontalBar'){
            // data[currChartIndex].showLegend=true;
            chart=new chartjs(ctx, {
                type: data[currChartIndex].chartType,
                data: {
                    labels: data[currChartIndex].labelsArr,
                    datasets: [{
                        label: 'Revenue',
                        backgroundColor: data[currChartIndex].colors,
                        borderColor: data[currChartIndex].borderColor,
                        borderWidth: data[currChartIndex].borderWidth,
                        data: data[currChartIndex].valuesArr
                    }]
                },
                options: {
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
                        enabled: !data[currChartIndex].showTooltip
                    },
                    legend: {
                        display: false
                    }
                },
                plugins: data[currChartIndex].showTooltip?[displayTooltip]:[]
            });
        }
        else if(data[currChartIndex].chartType=='line' || data[currChartIndex].chartType=='radar'){
            data[currChartIndex].showLegend=false;
            chart=new chartjs(ctx, {
                type: data[currChartIndex].chartType,
                data: {
                    labels: data[currChartIndex].labelsArr,
                    datasets: [{
                        label: 'Revenue',
                        backgroundColor: data[currChartIndex].lineRadarBackground,
                        borderColor: data[currChartIndex].borderColor,
                        borderWidth: data[currChartIndex].borderWidth,
                        data: data[currChartIndex].valuesArr
                    }]
                },
                options: {
                    title: {
                        display: false
                    },
                    tooltips: {
                        enabled: !data[currChartIndex].showTooltip
                    },
                    legend: {
                        display: false
                    }
                },
                plugins: data[currChartIndex].showTooltip?[displayTooltip]:[]
            });
        }
    }

    const displayTooltip={
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

    const addChart=()=>{
        data.push({
            chartType: 'pie',
            title: `Untitled`,
            titleSize: 20,
            titleColor: '#000000',
            labelsArr: ['Jan','Feb','March','April','June'],
            valuesArr: [1,2,3,4,5],
            colors: [],
            borderColor: '#000000',
            borderWidth: 2,
            lineRadarBackground: '#A1B2C3',
            legendColor: '#000000',
            legendStyle: 'vertical',
            legendBorderWidth: 2,
            legendBorderRadius: 2,
            legendBorderColor: '#dcdcdc',
            legendBackground: '#ABC123',
            legendPosition: 'left',
            showLegend: true,
            showTooltip: true,
            showValues: true,
            download: true
        });
        currChartIndex=data.length-1;
        handleSelect(data[currChartIndex].chartType);
    }

    const deleteChart=i=>{
        if(data.length>1){
            data.splice(i,1);
            data=[...data];
            currChartIndex=0;
            handleSelect(data[currChartIndex].chartType)
        }
    }

    const handleTitleBar=(e,i)=>{
        if(e.target.className== "fa-solid fa-times") return;
        if(data.length==1) return;
        currChartIndex=i;
        handleSelect(data[currChartIndex].chartType);
    }

    const handleSelect=(chartType)=>{
        data[currChartIndex].chartType=chartType;
        switch (chartType){
            case 'pie':
                currChartText='Pie chart';
                break;
            case 'doughnut':
                currChartText='Doughnut';
                break;
            case 'bar':
                currChartText='Vertical bar';
                break;
            case 'horizontalBar':
                currChartText='Horizontal bar';
                break;
            case 'line':
                currChartText='Line Graph';
                break;
            case 'radar':
                currChartText='Radar';
                break;
        }
        openSelect=false;
        handleChart();
    }

    const handleTitleSize=()=>{
        if(data[currChartIndex].titleSize>=37) data[currChartIndex].titleSize=37;
        if(data[currChartIndex].titleSize<=0) data[currChartIndex].titleSize=0;
    }

    const handleLabels=()=>{
        data[currChartIndex].labelsArr=data[currChartIndex].labels.split(',');
        data[currChartIndex].labelsArr=data[currChartIndex].labelsArr.filter(label=>label.length>0);
        chart.data.labels=data[currChartIndex].labelsArr;
        chart.update();
    }
    
    const handleValues=()=>{
        data[currChartIndex].valuesArr=data[currChartIndex].values.split(',');
        data[currChartIndex].valuesArr=data[currChartIndex].valuesArr.filter(value=>value.length>0);
        chart.data.datasets[0].data=data[currChartIndex].valuesArr;
        chart.update();
    }

    const handleBorderWidth=()=>{
        chart.data.datasets[0].borderWidth=data[currChartIndex].borderWidth;
        chart.update()
    }

    const handleBorder=e=>{
        data[currChartIndex].borderColor=e.target.value;
        chart.data.datasets[0].borderColor=data[currChartIndex].borderColor;
        chart.update();
    }

    const handleLineRadarBg=()=>{
        chart.data.datasets[0].backgroundColor=data[currChartIndex].lineRadarBackground;
        chart.update();
    }

    const handleColorsInp=(e,i)=>{
        data[currChartIndex].colors[i]=e.target.value;
        chart.data.datasets[0].backgroundColor=data[currChartIndex].colors;
        chart.update();
    }

    const handleDownload=(downloadType)=>{
        downloadAs=downloadType;
        download=true;

        setTimeout(()=>{
            download=false
        },2500)
    }

    const createColor=()=>{
        const possibilities="abcdef0123456789";

        return "#"+possibilities[Math.floor(Math.random()*16)]+possibilities[Math.floor(Math.random()*16)]
            +possibilities[Math.floor(Math.random()*16)]+possibilities[Math.floor(Math.random()*16)]
            +possibilities[Math.floor(Math.random()*16)]+possibilities[Math.floor(Math.random()*16)];
    }

    $: {
        data[currChartIndex].labelsArr.forEach((label,i)=>{
            if(!data[currChartIndex].colors[i]) data[currChartIndex].colors[i]=createColor();
        })

        downloadData.set(data);
    }
</script>

<!-- chart section -->
<div class="chart-sec">
    <!-- title bar section -->
    <div class="title-bar-sec">
        {#each data as indChart,i}
            <div class="ind-title-bar" id={`${currChartIndex==i?'active':''}`} on:click={e=>handleTitleBar(e,i)}>
                <span class="title-bar">{data[i].title?data[i].title:'Untitled'}</span>
                <i class="fa-solid fa-times" on:click={()=>deleteChart(i)}></i>
            </div>
        {/each}
        <div class="add-chart-sec">
            <i class="fa-solid fa-plus" on:click={addChart}></i>
        </div>
    </div>

    <!-- chart title section -->
    <h2 class="chart-title" style={`font-size: ${data[currChartIndex].titleSize}px; color: ${data[currChartIndex].titleColor}`} min=0 max=50>{data[currChartIndex].title}</h2>
    
    <!-- canvas section -->
    <canvas bind:this={canvas}></canvas>

    <!-- legend section -->
    {#if data[currChartIndex].showLegend}
    {#if data[currChartIndex].legendStyle=='vertical'}
        <div class="legend-sec" style={`display: ${data[currChartIndex].labelsArr.length>=1 ? 'block': 'none'}; background: ${data[currChartIndex].legendBackground}; color: ${data[currChartIndex].legendColor}; border: ${data[currChartIndex].legendBorderWidth}px solid ${data[currChartIndex].legendBorderColor}; border-radius: ${data[currChartIndex].legendBorderRadius}px; float: ${data[currChartIndex].legendPosition=='left'?'left':data[currChartIndex].legendPosition=='right'?'right':'none'}; margin: ${data[currChartIndex].legendPosition=='center'? '2rem auto 1rem': '2rem 1rem 1rem'}`}>
            <ul>
                {#each data[currChartIndex].labelsArr as label,index}
                    <li>
                        <div class="legend-label-color" style={`background-color:${data[currChartIndex].colors[index]}; margin-right: 1rem`}></div>
                        <div class="legend-label-text">{label}{data[currChartIndex].showValues && data[currChartIndex].valuesArr[index]?` (${data[currChartIndex].valuesArr[index]})`:''}</div>
                    </li>
                {/each}
            </ul>   
        </div>
    {:else if data[currChartIndex].legendStyle=='horizontal'}
         <div class="legend-sec-horizontal" style={`display: ${data[currChartIndex].labelsArr.length>=1 ? 'block': 'none'}`}>
            <ul>
                {#each data[currChartIndex].labelsArr as label,index}
                    <li>
                        <div class="legend-label-color" style={`background-color:${data[currChartIndex].colors[index]}`}></div>
                        <div class="legend-label-text">{label}{data[currChartIndex].showValues?` (${data[currChartIndex].valuesArr[index]})`:''}</div>
                    </li>
                {/each}
            </ul>   
        </div>
    {/if}
    {/if}
</div>

<!-- vertical line -->
<div class="line"></div>

<!-- input section -->
<div class="input-sec">
    <!-- select section -->
    <div class="select-sec">
        <div style={openSelect?'border-bottom: 2px solid #000':''} on:click={()=>openSelect=!openSelect} class="selected">
            <div class="text">{currChartText}</div>
            <div class="icon">
                <i class={openSelect?'fa-solid fa-chevron-up':'fa-solid fa-chevron-down'}></i>
            </div>
        </div>
        {#if openSelect}
            <ul on:click={e=>handleSelect(e.target.getAttribute('id'))}>
                <li id="pie">Pie chart</li>
                <li id="line">Line graph</li>
                <li id="horizontalBar">Horizontal bar</li>
                <li id="bar">Vertical bar</li>
                <li id="doughnut">Doughnut</li>
                <li id="radar">Radar</li>
            </ul>
        {/if}
    </div>

    <div class="horizontal-line"></div>

    <!-- title section -->
    <div class="title-sec">
        <span id="label">Title</span><br>
        <div style={openTitle?'border-bottom: 1px solid #000':''} class="title-inp">
            <input bind:value={data[currChartIndex].title} class="title" type="text" placeholder="eg: Countries by area">
            <div class="icon">
                <i on:click={()=>openTitle=!openTitle} class={openTitle?'fa-solid fa-chevron-up':'fa-solid fa-chevron-down'}></i>
            </div>
        </div>
        {#if openTitle}
            <div class="title-settings">
                <div class="title-font-sec">
                    <span>Size</span>
                    <input bind:value={data[currChartIndex].titleSize} on:input={handleTitleSize} type="range" min=0 max=37>
                </div>
                <div class="inner-line"></div>
                <div class="title-color-sec">
                    <span>Color</span>
                    <input bind:value={data[currChartIndex].titleColor} type="color">
                </div>
            </div>
        {/if}
    </div>

    <div class="horizontal-line"></div>

    <!-- labels section -->
    <div class="labels-sec">
        <span id="label">Labels</span><br>
        <input bind:value={data[currChartIndex].labels} on:input={handleLabels} class="labels" type="text" placeholder="eg: jan, feb, march, ...">
    </div>

    <div class="horizontal-line"></div>

    <!-- values section -->
    <div class="values-sec">
        <span id="label">Values</span><br>
        <input bind:value={data[currChartIndex].values} on:input={handleValues} class="values" type="text" placeholder="eg: 10, 20, 15, ...">
    </div>

    <div class="horizontal-line one-rem"></div>

    <div class="border-settings">
        <span id="label">Border</span>
        <div class="border-width-sec">
            <span>Width</span>
            <input type="range" min="0" max="5" step=".1" bind:value={data[currChartIndex].borderWidth} on:input={handleBorderWidth} class="slider">
        </div>
        <div class="inner-line"></div>
        <div class="border-color-sec">
            <span>Color</span>
            <input bind:value={data[currChartIndex].borderColor} on:input={handleBorder} class="line-radar-border" type="color">
        </div>
    </div>

    <div class="horizontal-line one-rem"></div>

    <!-- tooltip section -->
    <div class="tooltip-sec">
        <span id="label">Show tooltip</span>
        <div class="checkbox">
            <label class="switch">
                <input type="checkbox" bind:checked={data[currChartIndex].showTooltip} on:change={handleChart}>
                <span class="checkbox-slider round"></span>
            </label>
        </div>
    </div>

    <div class="horizontal-line"></div>

    <!-- colors section -->
    <div class="colors-sec">
        <div style={openColors?'border-bottom: 2px solid #000':''} on:click={()=>openColors=!openColors} class="color-text">
            <div class="text">Colors</div>
            <div class="icon">
                <i class={openColors?'fa-solid fa-chevron-up':'fa-solid fa-chevron-down'}></i>
            </div>
        </div>
        {#if openColors}
            <div class="colors-inp">
                {#if (data[currChartIndex].chartType=='line' || data[currChartIndex].chartType=='radar')}
                    <div class="line-radar-bg-sec">
                        <span>Background</span>
                        <input bind:value={data[currChartIndex].lineRadarBackground} on:input={handleLineRadarBg} class="line-radar-bg" type="color">
                    </div>
                {:else if openColors}
                    {#each data[currChartIndex].labelsArr as item,i}
                        <div class="ind-color">
                            <span>{item}</span>
                            <input on:input={e=>handleColorsInp(e,i)} type="color" value={data[currChartIndex].colors[i]?data[currChartIndex].colors[i]:'#f6ad35'}>
                        </div>
                        {#if i!=data[currChartIndex].labelsArr.length-1}
                            <div class="inner-line"></div>
                        {/if}
                    {/each}
                {/if}
            </div>
        {/if}
    </div>

    <div class="horizontal-line"></div>

    <!-- legend settings section -->
    {#if data[currChartIndex].showLegend}
        <div class="legend-settings-sec">
            <div style={openLegends?'border-bottom: 2px solid #000':''} on:click={()=>openLegends=!openLegends} class="legend-text">
                <div class="text">Legend</div>
                <div class="icon">
                    <i class={openLegends?'fa-solid fa-chevron-up':'fa-solid fa-chevron-down'}></i>
                </div>
            </div>
            {#if openLegends}
                <div class="legend-settings" style={`padding-bottom:${data[currChartIndex].legendStyle=='vertical'?'.5rem':''}`}>
                    <div class="legend-style-sec">
                        <span class="legend-span">Display</span>
                        <div class="legend-style-icons">
                            <div class={data[currChartIndex].legendStyle=='vertical'?'active':''} on:click={()=>data[currChartIndex].legendStyle='vertical'}>
                                <i class="fa-solid fa-arrows-up-down" style="padding: 0 .25rem"></i>
                            </div>
                            <div class={data[currChartIndex].legendStyle=='horizontal'?'active':''} on:click={()=>data[currChartIndex].legendStyle='horizontal'}>
                                <i class="fa-solid fa-arrows-left-right"></i>
                            </div>
                        </div>
                    </div>
                    {#if data[currChartIndex].legendStyle=='vertical'}
                        <div class="inner-line"></div>
                        <div class="legend-bg-sec">
                            <span class="legend-span">Background</span>
                            <input bind:value={data[currChartIndex].legendBackground} class="legend-bg" type="color">
                        </div>
                        <div class="legend-text-sec">
                            <span class="legend-span">Text</span>
                            <input bind:value={data[currChartIndex].legendColor} class="legend-text" type="color">
                        </div>
                         <div class="inner-line"></div>
                        <div class="legend-border-sec">
                            <span class="legend-span">Border</span>
                            <input bind:value={data[currChartIndex].legendBorderWidth} type="range" min=0 max=15>
                        </div>
                        <div class="legend-border-color-sec">
                            <span class="legend-span">Color</span>
                            <input bind:value={data[currChartIndex].legendBorderColor} class="legend-border-color" type="color">
                        </div>
                        <div class="legend-radius-sec">
                            <span class="legend-span">Radius</span>
                            <input bind:value={data[currChartIndex].legendBorderRadius} class="legend-radius-size" type="number">
                        </div>
                        <div class="inner-line"></div>
                        <div class="tooltip-sec">
                            <span class="show-values-txt legend-span">Show values</span>
                            <label class="switch">
                                <input type="checkbox" bind:checked={data[currChartIndex].showValues}>
                                <span class="checkbox-slider round"></span>
                            </label>
                        </div>
                        <div class="inner-line"></div>
                        <div class="legend-position-sec">
                            <span class="legend-span">Position</span>
                            <div class="legend-position-icons">
                                <div class={data[currChartIndex].legendPosition=='left'?'active':''} on:click={()=>data[currChartIndex].legendPosition='left'}>
                                    <i class="fa-solid fa-align-left"></i>
                                </div>
                                <div id="mid-position" class={data[currChartIndex].legendPosition=='center'?'active':''} on:click={()=>data[currChartIndex].legendPosition='center'}>
                                    <i class="fa-solid fa-align-center"></i>
                                </div>
                                <div class={data[currChartIndex].legendPosition=='right'?'active':''} on:click={()=>data[currChartIndex].legendPosition='right'}>
                                    <i class="fa-solid fa-align-right"></i>
                                </div>
                            </div>
                        </div>
                    {/if}
                </div>
            {/if}
        </div>
    {/if}
</div>
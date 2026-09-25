<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
  <title>南宁地铁1号线</title>
  <style>
    body { font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif; background: #fff; margin: 0; }
    .map-container { width: 100%; overflow: auto; }
    .map-svg { width: 100%; height: auto; }
    .line-path-1 { fill: none; stroke-width: 8; stroke-linecap: round; stroke-linejoin: round; stroke: #00A651; }
    .station-dot-1 { fill: #00A651; cursor: pointer; }
    .station-dot-1:hover { r: 10; }
    .transfer-dot-1 { fill: #fff; stroke: #00A651; stroke-width: 3; cursor: pointer; }
    .transfer-dot-1:hover { r: 10; }
    .line-path-2 { fill: none; stroke-width: 8; stroke-linecap: round; stroke-linejoin: round; stroke: #E53935; }
    .station-dot-2 { fill: #E53935; cursor: pointer; }
    .station-dot-2:hover { r: 10; }
    .transfer-dot-2 { fill: #fff; stroke: #E53935; stroke-width: 3; cursor: pointer; }
    .transfer-dot-2:hover { r: 10; }
    .line-path-3 { fill: none; stroke-width: 8; stroke-linecap: round; stroke-linejoin: round; stroke: #9966CC; }
    .station-dot-3 { fill: #9966CC; cursor: pointer; }
    .station-dot-3:hover { r: 10; }
    .transfer-dot-3 { fill: #fff; stroke: #9966CC; stroke-width: 3; cursor: pointer; }
    .transfer-dot-3:hover { r: 10; }
    .line-path-4 { fill: none; stroke-width: 8; stroke-linecap: round; stroke-linejoin: round; stroke: #C4CC23; }
    .station-dot-4 { fill: #C4CC23; cursor: pointer; }
    .station-dot-4:hover { r: 10; }
    .transfer-dot-4 { fill: #fff; stroke: #C4CC23; stroke-width: 3; cursor: pointer; }
    .transfer-dot-4:hover { r: 10; }
    .line-path-5 { fill: none; stroke-width: 8; stroke-linecap: round; stroke-linejoin: round; stroke: #3366CC; }
    .station-dot-5 { fill: #3366CC; cursor: pointer; }
    .station-dot-5:hover { r: 10; }
    .transfer-dot-5 { fill: #fff; stroke: #3366CC; stroke-width: 3; cursor: pointer; }
    .transfer-dot-5:hover { r: 10; }
    .line-path-6 { fill: none; stroke-width: 8; stroke-linecap: round; stroke-linejoin: round; stroke: #3FCED6; }
    .station-dot-6 { fill: #3FCED6; cursor: pointer; }
    .station-dot-6:hover { r: 10; }
    .transfer-dot-6 { fill: #fff; stroke: #3FCED6; stroke-width: 3; cursor: pointer; }
    .transfer-dot-6:hover { r: 10; }
    .line-path-7 { fill: none; stroke-width: 8; stroke-linecap: round; stroke-linejoin: round; stroke: #FF9D01; }
    .station-dot-7 { fill: #FF9D01; cursor: pointer; }
    .station-dot-7:hover { r: 10; }
    .transfer-dot-7 { fill: #fff; stroke: #FF9D01; stroke-width: 3; cursor: pointer; }
    .transfer-dot-7:hover { r: 10; }
    .station-name { font-size: 12px; fill: #333; text-anchor: middle; pointer-events: none; }
    .line-label { font-size: 12px; font-weight: bold; fill: #fff; text-anchor: middle; }
    .legend { position: fixed; bottom: 16px; left: 16px; background: rgba(255,255,255,0.95); padding: 12px; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1); z-index: 100; }
    .legend-item { display: flex; align-items: center; gap: 8px; margin: 4px 0; font-size: 12px; }
    .legend-color { width: 16px; height: 4px; border-radius: 2px; }
    .popup { position: fixed; background: rgba(0,0,0,0.85); color: #fff; padding: 12px 16px; border-radius: 8px; font-size: 14px; pointer-events: none; z-index: 200; display: none; }
    .popup-title { font-weight: bold; }
    .popup-transfer { font-size: 12px; color: #aaa; }
  </style>
</head>
<body>
  <div class="map-container" id="mapContainer">
    <svg class="map-svg" id="mapSvg" viewBox="0 -500 3600 1800" xmlns="http://www.w3.org/2000/svg"></svg>
  </div>
  <div class="legend">
    <div class="legend-item"><div class="legend-color" style="background:#00A651"></div><span>1号线</span></div>
    <div class="legend-item"><div class="legend-color" style="background:#E53935"></div><span>2号线</span></div>
    <div class="legend-item"><div class="legend-color" style="background:#9966CC"></div><span>3号线</span></div>
    <div class="legend-item"><div class="legend-color" style="background:#C4CC23"></div><span>4号线</span></div>
    <div class="legend-item"><div class="legend-color" style="background:#3366CC"></div><span>5号线</span></div>
    <div class="legend-item"><div class="legend-color" style="background:#3FCED6"></div><span>机场线</span></div>
    <div class="legend-item"><div class="legend-color" style="background:#FF9D01"></div><span>6号线</span></div>
  </div>
  <div class="popup" id="popup"></div>

  <script>
    const svg = document.getElementById('mapSvg');
    const popup = document.getElementById('popup');
    const container = document.getElementById('mapContainer');

    // 拐弯点坐标
    const stationCoords = {
      trainStation: { x: 1000, y: 100 },   // 火车站（拐弯点）
      chaoyangSquare: { x: 1000, y: 170 }   // 朝阳广场站（拐弯点）
    };

    // ========== 1号线（绿色 #00A651）- 水平→垂直→水平 ==========
    const line1Stations = [
      // 第一段：水平向右到火车站
      { name: '石埠站', x: 100, y: 100 },
      { name: '南职院站', x: 200, y: 100 },
      { name: '鹏飞路站', x: 300, y: 100 },
      { name: '西乡塘客运站', x: 400, y: 100 },
      { name: '民族大学站', x: 500, y: 100 },
      { name: '清川站', x: 600, y: 100 },
      { name: '动物园站', x: 650, y: 100 },
      { name: '鲁班路站', x: 750, y: 100 },
      { name: '广西大学站', x: 850, y: 100, transfer: true },
      { name: '白苍岭站', x: 950, y: 100 },
      // 拐弯点：火车站
      { name: '火车站', x: stationCoords.trainStation.x, y: stationCoords.trainStation.y, transfer: true },
      // 第二段：垂直向下到朝阳广场站
      { name: '朝阳广场站', x: stationCoords.chaoyangSquare.x, y: stationCoords.chaoyangSquare.y, transfer: true },
      // 第三段：水平向右到火车东站
      { name: '新民路站', x: 1150, y: 170 },
      { name: '民族广场站', x: 1300, y: 170 },
      { name: '麻村站', x: 1450, y: 170 },
      { name: '南湖站', x: 1600, y: 170 },
      { name: '金湖广场站', x: 1750, y: 170, transfer: true },
      { name: '会展中心站', x: 1900, y: 170 },
      { name: '万象城站', x: 2050, y: 170 },
      { name: '东盟商务区站', x: 2200, y: 170 },
      { name: '凤岭站', x: 2350, y: 170 },
      { name: '埌东客运站', x: 2500, y: 170, transfer: true },
      { name: '百花岭站', x: 2650, y: 123 },
      { name: '佛子岭站', x: 2650, y: 80 },
      { name: '火车东站', x: 2530, y: 50 },
      { name: '长虹路站', x: 2400, y: -50 },
      { name: '金桥客运站', x: 2400, y: -150, transfer: true },
    ];

    // 绘制1号线线路
    const path1 = document.createElementNS('http://www.w3.org/2000/svg', 'path');
    const { trainStation, chaoyangSquare } = stationCoords;
    path1.setAttribute('d', `M100,100 L${trainStation.x},100 L${chaoyangSquare.x},${chaoyangSquare.y} L2600,${chaoyangSquare.y} C2600,${chaoyangSquare.y} 2650,${chaoyangSquare.y} 2650,140 L2650,50 L2400,50 L2400,-150`);
    path1.setAttribute('class', 'line-path-1');
    svg.appendChild(path1);

    // 绘制1号线站点
    line1Stations.forEach((station, index) => {
      // 金湖广场站由3号线绘制，1号线跳过
      if (station.name === '金湖广场站') return;
      // 金桥客运站由5号线绘制，1号线跳过
      if (station.name === '金桥客运站') return;
      // 广西大学站由5号线绘制，1号线跳过
      if (station.name === '广西大学站') return;
      // 埌东客运站由6号线绘制，1号线跳过
      if (station.name === '埌东客运站') return;

      const circle = document.createElementNS('http://www.w3.org/2000/svg', 'circle');
      circle.setAttribute('cx', station.x);
      circle.setAttribute('cy', station.y);
      circle.setAttribute('r', station.transfer ? 8 : 6);
      circle.setAttribute('class', station.transfer ? 'transfer-dot-1' : 'station-dot-1');
      svg.appendChild(circle);

      // 根据站点位置决定名称放置方向，避免在线路上
      const text = document.createElementNS('http://www.w3.org/2000/svg', 'text');
      text.textContent = station.name;

      // 火车东站附近的垂直站点（长虹路站、金桥客运站）
      if (station.x === 2400 && station.y < 50) {
        text.setAttribute('x', station.x - 38);
        text.setAttribute('y', station.y + 5);
        text.setAttribute('text-anchor', 'start');
      }
      // 佛子岭站
      else if (station.x === 2650 && station.y === 80) {
        text.setAttribute('x', station.x - 30);
        text.setAttribute('y', station.y + 5);
        text.setAttribute('text-anchor', 'start');
      }
      // 百花岭站
      else if (station.x === 2650 && station.y === 123) {
        text.setAttribute('x', station.x - 30);
        text.setAttribute('y', station.y + 5);
        text.setAttribute('text-anchor', 'start');
      }
      // 火车站和朝阳广场站放在上方
      else if (station.x === stationCoords.trainStation.x && station.y === stationCoords.trainStation.y) {
        text.setAttribute('x', station.x + 30);
        text.setAttribute('y', station.y - 15);
        text.setAttribute('text-anchor', 'middle');
      } else if (station.x === stationCoords.chaoyangSquare.x && station.y === stationCoords.chaoyangSquare.y) {
        text.setAttribute('x', station.x + 30);
        text.setAttribute('y', station.y - 15);
        text.setAttribute('text-anchor', 'middle');
      } else if (station.name === '金湖广场站') {
        // 金湖广场站：由3号线绘制，1号线跳过
      } else if (index <= 15 || index >= 17) {
        // 水平段站点：文字一上一下交替
        const isEven = index % 2 === 0;
        text.setAttribute('x', station.x);
        text.setAttribute('y', isEven ? station.y - 15 : station.y + 24);
        text.setAttribute('text-anchor', 'middle');
      } else {
        text.setAttribute('x', station.x);
        text.setAttribute('y', station.y + 24);
        text.setAttribute('text-anchor', 'middle');
      }
      text.setAttribute('class', 'station-name');
      svg.appendChild(text);

      circle.addEventListener('click', () => {
        popup.innerHTML = `<div class="popup-title">${station.name}</div><div class="popup-transfer">1号线${station.transfer ? '（换乘站）' : ''}</div>`;
        popup.style.left = `${station.x + 20}px`;
        popup.style.top = `${station.y - 30}px`;
        popup.style.display = 'block';
        setTimeout(() => popup.style.display = 'none', 3000);
      });
    });

    // 1号线标签
    const labelBg1 = document.createElementNS('http://www.w3.org/2000/svg', 'rect');
    labelBg1.setAttribute('x', 60);
    labelBg1.setAttribute('y', 86);
    labelBg1.setAttribute('width', 22);
    labelBg1.setAttribute('height', 22);
    labelBg1.setAttribute('rx', 11);
    labelBg1.setAttribute('fill', '#00A651');
    svg.appendChild(labelBg1);

    const labelText1 = document.createElementNS('http://www.w3.org/2000/svg', 'text');
    labelText1.setAttribute('x', 71);
    labelText1.setAttribute('y', 99);
    labelText1.setAttribute('class', 'line-label');
    labelText1.textContent = '1';
    svg.appendChild(labelText1);

    // ========== 2号线（红色 #E53935）- 西津站→火车站→朝阳广场站→金象站→东段延伸 ==========
    const line2Stations = [
      // 北段：火车站向上延伸
      { name: '西津站', x: stationCoords.trainStation.x, y: -380 },
      { name: '安吉客运站', x: stationCoords.trainStation.x, y: -300 },
      { name: '苏卢站', x: stationCoords.trainStation.x, y: -220 },
      { name: '三十三中站', x: stationCoords.trainStation.x, y: -140 },
      { name: '秀厢站', x: stationCoords.trainStation.x, y: -60 },
      { name: '明秀路站', x: stationCoords.trainStation.x, y: 20, transfer: true },
      { name: '火车站', x: stationCoords.trainStation.x, y: stationCoords.trainStation.y, transfer: true },
      // 南段：朝阳广场站向下延伸（福建园对齐埌西站y=275，其余均匀分布）
      { name: '朝阳广场站', x: stationCoords.chaoyangSquare.x, y: stationCoords.chaoyangSquare.y, transfer: true },
      { name: '南宁剧场站', x: stationCoords.chaoyangSquare.x, y: 223 },
      { name: '福建园站', x: stationCoords.chaoyangSquare.x, y: 275, transfer: true },
      { name: '亭洪路站', x: stationCoords.chaoyangSquare.x, y: 363 },
      { name: '石柱岭站', x: stationCoords.chaoyangSquare.x, y: 451 },
      { name: '江南客运站', x: stationCoords.chaoyangSquare.x, y: 539 },
      { name: '大沙田站', x: stationCoords.chaoyangSquare.x, y: 627, transfer: true },
      { name: '建设路站', x: stationCoords.chaoyangSquare.x, y: 715 },
      { name: '石子塘站', x: stationCoords.chaoyangSquare.x, y: 803 },
      { name: '金象站', x: stationCoords.chaoyangSquare.x, y: 890 },
      // 东段：金象站向下→直角向右延伸
      { name: '玉洞站', x: 1150, y: 950 },
      { name: '东风路站', x: 1300, y: 950 },
      { name: '玉岭路站', x: 1450, y: 950 },
      { name: '那福路站', x: 1600, y: 950 },
      { name: '平良立交站', x: 1750, y: 950, transfer: true },
      { name: '坛泽站', x: 1900, y: 950 },
      { name: '淡坛坡站', x: 2050, y: 950 },
      { name: '坛沙站', x: 2200, y: 950 },
    ];

    // 绘制2号线线路
    const path2 = document.createElementNS('http://www.w3.org/2000/svg', 'path');
    path2.setAttribute('d', `M${stationCoords.trainStation.x},-380 L${stationCoords.trainStation.x},${stationCoords.trainStation.y} M${stationCoords.chaoyangSquare.x},${stationCoords.chaoyangSquare.y} L${stationCoords.chaoyangSquare.x},890 L${stationCoords.chaoyangSquare.x},950 L2200,950`);
    path2.setAttribute('class', 'line-path-2');
    svg.appendChild(path2);

    // 绘制2号线站点
    line2Stations.forEach((station, index) => {
      // 大沙田站由4号线绘制，2号线跳过
      if (station.name === '大沙田站') return;
      // 明秀路站由5号线绘制，2号线跳过
      if (station.name === '明秀路站') return;
      // 福建园站由6号线绘制，2号线跳过
      if (station.name === '福建园站') return;

      const circle = document.createElementNS('http://www.w3.org/2000/svg', 'circle');
      circle.setAttribute('cx', station.x);
      circle.setAttribute('cy', station.y);
      circle.setAttribute('r', station.transfer ? 8 : 6);
      circle.setAttribute('class', station.transfer ? 'transfer-dot-2' : 'station-dot-2');
      svg.appendChild(circle);

      if (station.name !== '朝阳广场站' && station.name !== '火车站') {
        const text = document.createElementNS('http://www.w3.org/2000/svg', 'text');
        text.textContent = station.name;

        if (station.y < stationCoords.trainStation.y) {
          // 北段站点文字
          if (station.name === '安吉客运站') {
            text.setAttribute('x', station.x + 40);
            text.setAttribute('y', station.y + 30);
            text.setAttribute('text-anchor', 'start');
          } else if (station.name === '苏卢站' || station.name === '三十三中站' || station.name === '秀厢站') {
            // 苏卢、三十三中、秀厢：文字统一放在左侧
            text.setAttribute('x', station.x - 30);
            text.setAttribute('y', station.y + 5);
            text.setAttribute('text-anchor', 'end');
          } else {
            text.setAttribute('x', station.x + 30);
            text.setAttribute('y', station.y + 5);
            text.setAttribute('text-anchor', 'start');
          }
        } else if (station.y > stationCoords.chaoyangSquare.y && station.y <= 900) {
          // 南段站点（金象站及以上）
          text.setAttribute('x', station.x - 30);
          text.setAttribute('y', station.y + 5);
          text.setAttribute('text-anchor', 'end');
        } else if (station.y === 950) {
          // 东段站点（金象站向右延伸）- 文字一上一下交替
          // 平良立交与3号线交汇，文字向右偏移避免压线
          const isEven = index % 2 === 0;
          if (station.name === '平良立交站') {
            text.setAttribute('x', station.x + 35);
            text.setAttribute('y', station.y + 24);
            text.setAttribute('text-anchor', 'start');
          } else {
            text.setAttribute('x', station.x);
            text.setAttribute('y', isEven ? station.y - 15 : station.y + 24);
            text.setAttribute('text-anchor', 'middle');
          }
        } else {
          text.setAttribute('x', station.x);
          text.setAttribute('y', station.y - 15);
          text.setAttribute('text-anchor', 'middle');
        }
        text.setAttribute('class', 'station-name');
        svg.appendChild(text);
      }

      circle.addEventListener('click', () => {
        popup.innerHTML = `<div class="popup-title">${station.name}</div><div class="popup-transfer">2号线${station.transfer ? '（换乘站）' : ''}</div>`;
        popup.style.left = `${station.x + 20}px`;
        popup.style.top = `${station.y - 30}px`;
        popup.style.display = 'block';
        setTimeout(() => popup.style.display = 'none', 3000);
      });
    });

    // 2号线标签（西津站旁边）
    const labelBg2Start = document.createElementNS('http://www.w3.org/2000/svg', 'rect');
    labelBg2Start.setAttribute('x', 940);
    labelBg2Start.setAttribute('y', -394);
    labelBg2Start.setAttribute('width', 22);
    labelBg2Start.setAttribute('height', 22);
    labelBg2Start.setAttribute('rx', 11);
    labelBg2Start.setAttribute('fill', '#E53935');
    svg.appendChild(labelBg2Start);

    const labelText2Start = document.createElementNS('http://www.w3.org/2000/svg', 'text');
    labelText2Start.setAttribute('x', 951);
    labelText2Start.setAttribute('y', -381);
    labelText2Start.setAttribute('class', 'line-label');
    labelText2Start.textContent = '2';
    svg.appendChild(labelText2Start);

    // 2号线标签（坛沙站旁边）
    const labelBg2End = document.createElementNS('http://www.w3.org/2000/svg', 'rect');
    labelBg2End.setAttribute('x', 2230);
    labelBg2End.setAttribute('y', 936);
    labelBg2End.setAttribute('width', 22);
    labelBg2End.setAttribute('height', 22);
    labelBg2End.setAttribute('rx', 11);
    labelBg2End.setAttribute('fill', '#E53935');
    svg.appendChild(labelBg2End);

    const labelText2End = document.createElementNS('http://www.w3.org/2000/svg', 'text');
    labelText2End.setAttribute('x', 2241);
    labelText2End.setAttribute('y', 949);
    labelText2End.setAttribute('class', 'line-label');
    labelText2End.textContent = '2';
    svg.appendChild(labelText2End);

    // ========== 3号线（紫色 #9966CC）- 科园大道→大鸡村→圆角拐弯→金湖广场→向下延伸 ==========
    const line3Stations = [
      // 第一段：水平向右（科园大道→大鸡村）
      { name: '科园大道站', x: 700, y: -300 },
      { name: '创业路站', x: 850, y: -300 },
      { name: '安吉客运站', x: stationCoords.trainStation.x, y: -300, transfer: true },
      { name: '北湖北路站', x: 1200, y: -300 },
      { name: '秀峰路站', x: 1350, y: -300 },
      { name: '邕武路站', x: 1500, y: -300 },
      { name: '大鸡村站', x: 1650, y: -300 },
      // 第二段：大鸡村向右延伸后圆角拐弯向下，拐弯处无站点
      // 第三段：垂直向下依次排列到金湖广场
      { name: '兴桂路站', x: 1750, y: -220 },
      { name: '小鸡村站', x: 1750, y: -150, transfer: true },
      { name: '东沟岭站', x: 1750, y: -80 },
      { name: '长堽路站', x: 1750, y: -10 },
      { name: '东葛路站', x: 1750, y: 60 },
      { name: '滨湖路站', x: 1750, y: 130 },
      // 金湖广场站（3号线换乘点，与1号线交汇处）
      { name: '金湖广场站', x: 1750, y: 170, transfer: true },
      // 第四段：从金湖广场继续向下延伸
      { name: '埌西站', x: 1750, y: 275, transfer: true },
      { name: '青竹立交站', x: 1750, y: 350 },
      { name: '青秀山站', x: 1750, y: 425 },
      { name: '市博物馆站', x: 1750, y: 500 },
      { name: '飞龙路站', x: 1750, y: 575 },
      { name: '总部基地站', x: 1750, y: 627, transfer: true, skipAll: true },
      { name: '广西规划馆站', x: 1750, y: 725 },
      { name: '庆歌路站', x: 1750, y: 800 },
      { name: '五象湖站', x: 1750, y: 875 },
      // 终点：平良立交站（与2号线换乘，文字由2号线绘制）
      { name: '平良立交站', x: 1750, y: 950, transfer: true, skipText: true },
      // 第五段：从平良立交继续向下延伸到终点
      { name: '保税中心北站', x: 1750, y: 1025 },
      { name: '保税中心站', x: 1750, y: 1100 },
      { name: '保税中心南站', x: 1750, y: 1175 },
    ];

    // 绘制3号线线路（水平段+圆角拐弯+垂直段→终点）
    const path3 = document.createElementNS('http://www.w3.org/2000/svg', 'path');
    path3.setAttribute('d', `M700,-300 L1650,-300 Q1750,-300 1750,-220 L1750,1175`);
    path3.setAttribute('class', 'line-path-3');
    svg.appendChild(path3);

    // 绘制3号线站点
    line3Stations.forEach((station, index) => {
      // 总部基地站由4号线绘制，3号线跳过
      if (station.skipAll) return;
      // 小鸡村站由5号线绘制，3号线跳过
      if (station.name === '小鸡村站') return;
      // 埌西站由6号线绘制，3号线跳过
      if (station.name === '埌西站') return;

      const circle = document.createElementNS('http://www.w3.org/2000/svg', 'circle');
      circle.setAttribute('cx', station.x);
      circle.setAttribute('cy', station.y);
      circle.setAttribute('r', station.transfer ? 8 : 6);
      circle.setAttribute('class', station.transfer ? 'transfer-dot-3' : 'station-dot-3');
      svg.appendChild(circle);

      // 跳过安吉客运站（由2号线绘制文字）和金湖广场站（由1号线绘制文字）
      if (station.name !== '安吉客运站' && !station.skipText) {
        const text = document.createElementNS('http://www.w3.org/2000/svg', 'text');
        text.textContent = station.name;

        if (index <= 6) {
          // 水平段站点：文字一上一下交替
          const isEven = index % 2 === 0;
          text.setAttribute('x', station.x);
          text.setAttribute('y', isEven ? station.y - 15 : station.y + 24);
          text.setAttribute('text-anchor', 'middle');
        } else if (station.name === '金湖广场站') {
          // 金湖广场站：换乘点，文字放到右下方不压线
          text.setAttribute('x', station.x + 35);
          text.setAttribute('y', station.y + 24);
          text.setAttribute('text-anchor', 'start');
        } else {
          // 垂直段站点（兴桂路→保税中心南）：文字统一放在右侧
          text.setAttribute('x', station.x + 43);
          text.setAttribute('y', station.y + 5);
          text.setAttribute('text-anchor', 'start');
        }
        text.setAttribute('class', 'station-name');
        svg.appendChild(text);
      }

      circle.addEventListener('click', () => {
        popup.innerHTML = `<div class="popup-title">${station.name}</div><div class="popup-transfer">3号线${station.transfer ? '（换乘站）' : ''}</div>`;
        popup.style.left = `${station.x + 20}px`;
        popup.style.top = `${station.y - 30}px`;
        popup.style.display = 'block';
        setTimeout(() => popup.style.display = 'none', 3000);
      });
    });

    // 3号线标签（放在科园大道站旁边）
    const labelBg3 = document.createElementNS('http://www.w3.org/2000/svg', 'rect');
    labelBg3.setAttribute('x', 650);
    labelBg3.setAttribute('y', -314);
    labelBg3.setAttribute('width', 22);
    labelBg3.setAttribute('height', 22);
    labelBg3.setAttribute('rx', 11);
    labelBg3.setAttribute('fill', '#9966CC');
    svg.appendChild(labelBg3);

    const labelText3 = document.createElementNS('http://www.w3.org/2000/svg', 'text');
    labelText3.setAttribute('x', 661);
    labelText3.setAttribute('y', -301);
    labelText3.setAttribute('class', 'line-label');
    labelText3.textContent = '3';
    svg.appendChild(labelText3);

    // 3号线标签（放在终点站保税中心南站旁边，避开文字）
    const labelBg3End = document.createElementNS('http://www.w3.org/2000/svg', 'rect');
    labelBg3End.setAttribute('x', 1810);
    labelBg3End.setAttribute('y', 1188);
    labelBg3End.setAttribute('width', 22);
    labelBg3End.setAttribute('height', 22);
    labelBg3End.setAttribute('rx', 11);
    labelBg3End.setAttribute('fill', '#9966CC');
    svg.appendChild(labelBg3End);

    const labelText3End = document.createElementNS('http://www.w3.org/2000/svg', 'text');
    labelText3End.setAttribute('x', 1821);
    labelText3End.setAttribute('y', 1201);
    labelText3End.setAttribute('class', 'line-label');
    labelText3End.textContent = '3';
    svg.appendChild(labelText3End);

    // ========== 4号线（黄绿色 #C4CC23）- 洪运路→大沙田→总部基地 ==========
    const line4Stations = [
      { name: '洪运路站', x: 680, y: 627 },
      { name: '那历村站', x: 765, y: 627 },
      { name: '那洪立交站', x: 850, y: 627, transfer: true },
      { name: '金阳路站', x: 900, y: 627 },
      { name: '通源路站', x: 950, y: 627 },
      { name: '大沙田站', x: 1000, y: 627, transfer: true },
      { name: '金象大道站', x: 1150, y: 627 },
      { name: '五象岭站', x: 1350, y: 627 },
      { name: '玉象路站', x: 1550, y: 627 },
      { name: '总部基地站', x: 1750, y: 627, transfer: true },
      { name: '飞龙路', x: 1900, y: 627 },
      { name: '体育中心西', x: 2020, y: 627 },
      { name: '体育中心东', x: 2140, y: 627 },
      { name: '良庆大桥南', x: 2260, y: 627 },
      { name: '良庆圩', x: 2380, y: 627 },
      { name: '楞塘村', x: 2500, y: 627 },
      { name: '五象火车站', x: 2620, y: 627 },
      { name: '清平坡', x: 2740, y: 627 },
      { name: '龙岗', x: 2860, y: 627 },
    ];

    // 绘制4号线线路
    const path4 = document.createElementNS('http://www.w3.org/2000/svg', 'path');
    path4.setAttribute('d', 'M680,627 L2860,627');
    path4.setAttribute('class', 'line-path-4');
    svg.appendChild(path4);

    // 绘制4号线站点
    line4Stations.forEach((station, index) => {
      // 那洪立交站由5号线绘制，4号线跳过
      if (station.name === '那洪立交站') return;
      const circle = document.createElementNS('http://www.w3.org/2000/svg', 'circle');
      circle.setAttribute('cx', station.x);
      circle.setAttribute('cy', station.y);
      circle.setAttribute('r', station.transfer ? 8 : 6);
      circle.setAttribute('class', station.transfer ? 'transfer-dot-4' : 'station-dot-4');
      svg.appendChild(circle);

      const text = document.createElementNS('http://www.w3.org/2000/svg', 'text');
      text.textContent = station.name;
      if (station.name === '大沙田站') {
        text.setAttribute('x', station.x + 28);
        text.setAttribute('y', station.y + 24);
        text.setAttribute('text-anchor', 'start');
      } else if (station.name === '总部基地站') {
        text.setAttribute('x', station.x + 35);
        text.setAttribute('y', station.y + 24);
        text.setAttribute('text-anchor', 'start');
      } else {
        const isEven = index % 2 === 0;
        text.setAttribute('x', station.x);
        text.setAttribute('y', isEven ? station.y - 15 : station.y + 24);
        text.setAttribute('text-anchor', 'middle');
      }
      text.setAttribute('class', 'station-name');
      svg.appendChild(text);

      circle.addEventListener('click', () => {
        popup.innerHTML = `<div class="popup-title">${station.name}</div><div class="popup-transfer">4号线${station.transfer ? '（换乘站）' : ''}</div>`;
        popup.style.left = `${station.x + 20}px`;
        popup.style.top = `${station.y - 30}px`;
        popup.style.display = 'block';
        setTimeout(() => popup.style.display = 'none', 3000);
      });
    });

    // 4号线标签（放在洪运路站旁边，起始点）
    const labelBg4 = document.createElementNS('http://www.w3.org/2000/svg', 'rect');
    labelBg4.setAttribute('x', 630);
    labelBg4.setAttribute('y', 613);
    labelBg4.setAttribute('width', 22);
    labelBg4.setAttribute('height', 22);
    labelBg4.setAttribute('rx', 11);
    labelBg4.setAttribute('fill', '#C4CC23');
    svg.appendChild(labelBg4);

    const labelText4 = document.createElementNS('http://www.w3.org/2000/svg', 'text');
    labelText4.setAttribute('x', 641);
    labelText4.setAttribute('y', 626);
    labelText4.setAttribute('class', 'line-label');
    labelText4.textContent = '4';
    svg.appendChild(labelText4);

    // 4号线标签（放在龙岗站旁边，终点）
    const labelBg4End = document.createElementNS('http://www.w3.org/2000/svg', 'rect');
    labelBg4End.setAttribute('x', 2890);
    labelBg4End.setAttribute('y', 613);
    labelBg4End.setAttribute('width', 22);
    labelBg4End.setAttribute('height', 22);
    labelBg4End.setAttribute('rx', 11);
    labelBg4End.setAttribute('fill', '#C4CC23');
    svg.appendChild(labelBg4End);

    const labelText4End = document.createElementNS('http://www.w3.org/2000/svg', 'text');
    labelText4End.setAttribute('x', 2901);
    labelText4End.setAttribute('y', 626);
    labelText4End.setAttribute('class', 'line-label');
    labelText4End.textContent = '4';
    svg.appendChild(labelText4End);

    // ========== 5号线（蓝色 #3366CC）- 小鸡村→嘉和城 ==========
    const line5Stations = [
      { name: '小鸡村站', x: 1750, y: -150, transfer: true },
      { name: '邕宾立交', x: 1970, y: -150 },
      { name: '降桥', x: 2190, y: -150 },
      { name: '金桥客运站', x: 2400, y: -150, transfer: true },
      { name: '金桥物流园', x: 2550, y: -150 },
      { name: '三塘', x: 2700, y: -150 },
      { name: '九曲湾', x: 2850, y: -150 },
      { name: '嘉和城南', x: 3000, y: -150 },
      { name: '嘉和城', x: 3150, y: -150 },
    ];

    // 绘制5号线线路
    const path5 = document.createElementNS('http://www.w3.org/2000/svg', 'path');
    path5.setAttribute('d', 'M1650,-150 L3150,-150');
    path5.setAttribute('class', 'line-path-5');
    svg.appendChild(path5);

    // 绘制5号线站点
    line5Stations.forEach((station, index) => {
      // 小鸡村站由5号线绘制圆点和文字
      const circle = document.createElementNS('http://www.w3.org/2000/svg', 'circle');
      circle.setAttribute('cx', station.x);
      circle.setAttribute('cy', station.y);
      circle.setAttribute('r', station.transfer ? 8 : 6);
      circle.setAttribute('class', station.transfer ? 'transfer-dot-5' : 'station-dot-5');
      svg.appendChild(circle);

      const text = document.createElementNS('http://www.w3.org/2000/svg', 'text');
      text.textContent = station.name;

      if (station.name === '小鸡村站' || station.name === '金桥客运站') {
        text.setAttribute('x', station.x + 33);
        text.setAttribute('y', station.y + 25);
        text.setAttribute('text-anchor', 'start');
      } else {
        const isEven = index % 2 === 0;
        text.setAttribute('x', station.x);
        text.setAttribute('y', isEven ? station.y - 15 : station.y + 24);
        text.setAttribute('text-anchor', 'middle');
      }
      text.setAttribute('class', 'station-name');
      svg.appendChild(text);

      circle.addEventListener('click', () => {
        popup.innerHTML = `<div class="popup-title">${station.name}</div><div class="popup-transfer">5号线${station.transfer ? '（换乘站）' : ''}</div>`;
        popup.style.left = `${station.x + 20}px`;
        popup.style.top = `${station.y - 30}px`;
        popup.style.display = 'block';
        setTimeout(() => popup.style.display = 'none', 3000);
      });
    });

    // 5号线标签（放在嘉和城站旁边，起始点）
    const labelBg5 = document.createElementNS('http://www.w3.org/2000/svg', 'rect');
    labelBg5.setAttribute('x', 3180);
    labelBg5.setAttribute('y', -164);
    labelBg5.setAttribute('width', 22);
    labelBg5.setAttribute('height', 22);
    labelBg5.setAttribute('rx', 11);
    labelBg5.setAttribute('fill', '#3366CC');
    svg.appendChild(labelBg5);

    const labelText5 = document.createElementNS('http://www.w3.org/2000/svg', 'text');
    labelText5.setAttribute('x', 3191);
    labelText5.setAttribute('y', -151);
    labelText5.setAttribute('class', 'line-label');
    labelText5.textContent = '5';
    svg.appendChild(labelText5);

    // 5号线 - 明秀路站支线（从明秀路向右延伸 100px）
    const path5Branch = document.createElementNS('http://www.w3.org/2000/svg', 'path');
    path5Branch.setAttribute('d', 'M1000,20 L1100,20');
    path5Branch.setAttribute('class', 'line-path-5');
    svg.appendChild(path5Branch);

    // 5号线 - 明秀路向左延伸至广西大学（先水平左移到 850，再圆角拐弯上移至 100）
    const path5Left = document.createElementNS('http://www.w3.org/2000/svg', 'path');
    path5Left.setAttribute('d', 'M1000,20 L850,20 Q850,20 850,100');
    path5Left.setAttribute('class', 'line-path-5');
    svg.appendChild(path5Left);

    // 5号线 - 广西大学向下延伸至那丹
    const path5Down = document.createElementNS('http://www.w3.org/2000/svg', 'path');
    path5Down.setAttribute('d', 'M850,100 L850,800');
    path5Down.setAttribute('class', 'line-path-5');
    svg.appendChild(path5Down);

    // 5号线 - 连接明秀路支线和小鸡村主线
    const path5Connect = document.createElementNS('http://www.w3.org/2000/svg', 'path');
    path5Connect.setAttribute('d', 'M1100,20 L1650,-150');
    path5Connect.setAttribute('class', 'line-path-5');
    svg.appendChild(path5Connect);

    // ========== 5号线站点圆点和文字（全部在路径之后绘制，确保换乘点覆盖在线上） ==========

    // 5号线 - 秀灵路站（明秀路左移线段上）
    const xiulingCircle = document.createElementNS('http://www.w3.org/2000/svg', 'circle');
    xiulingCircle.setAttribute('cx', 925);
    xiulingCircle.setAttribute('cy', 20);
    xiulingCircle.setAttribute('r', 6);
    xiulingCircle.setAttribute('class', 'station-dot-5');
    svg.appendChild(xiulingCircle);

    const xiulingText = document.createElementNS('http://www.w3.org/2000/svg', 'text');
    xiulingText.setAttribute('x', 925);
    xiulingText.setAttribute('y', 2);
    xiulingText.setAttribute('text-anchor', 'middle');
    xiulingText.setAttribute('class', 'station-name');
    xiulingText.textContent = '秀灵路站';
    svg.appendChild(xiulingText);

    xiulingCircle.addEventListener('click', () => {
      popup.innerHTML = `<div class="popup-title">秀灵路站</div><div class="popup-transfer">5号线</div>`;
      popup.style.left = '945px';
      popup.style.top = '-10px';
      popup.style.display = 'block';
      setTimeout(() => popup.style.display = 'none', 3000);
    });

    // 5号线 - 广西大学站（换乘站，由5号线绘制）
    // 【文字调整代码】修改 x/y 值调整位置
    const guangxiUnivCircle = document.createElementNS('http://www.w3.org/2000/svg', 'circle');
    guangxiUnivCircle.setAttribute('cx', 850);
    guangxiUnivCircle.setAttribute('cy', 100);
    guangxiUnivCircle.setAttribute('r', 8);
    guangxiUnivCircle.setAttribute('class', 'transfer-dot-5');
    svg.appendChild(guangxiUnivCircle);

    const guangxiUnivText = document.createElementNS('http://www.w3.org/2000/svg', 'text');
    guangxiUnivText.setAttribute('x', 805);       // ← 文字 x 调整
    guangxiUnivText.setAttribute('y', 90);        // ← 文字 y 调整
    guangxiUnivText.setAttribute('text-anchor', 'middle'); // ← 对齐方式
    guangxiUnivText.setAttribute('class', 'station-name');
    guangxiUnivText.textContent = '广西大学站';
    svg.appendChild(guangxiUnivText);

    guangxiUnivCircle.addEventListener('click', () => {
      popup.innerHTML = `<div class="popup-title">广西大学站</div><div class="popup-transfer">5号线（换乘站）</div>`;
      popup.style.left = '870px';
      popup.style.top = '70px';
      popup.style.display = 'block';
      setTimeout(() => popup.style.display = 'none', 3000);
    });

    // 5号线 - 广西大学向下延伸站点
    const line5DownStations = [
      { name: '新秀公园', x: 850, y: 200 },
      // 【五一立交文字调整代码】
      { name: '五一立交', x: 850, y: 275, transfer: true },
      { name: '周家坡', x: 850, y: 400 },
      { name: '江南公园', x: 850, y: 500 },
      { name: '金凯路', x: 850, y: 565 },
      // 【那洪立交文字调整代码】
      { name: '那洪立交站', x: 850, y: 627, transfer: true },
      { name: '国凯大道', x: 850, y: 700 },
      { name: '那罗', x: 850, y: 800 },
    ];

    line5DownStations.forEach((station) => {
      // 五一立交由6号线绘制，5号线跳过
      if (station.name === '五一立交') return;
      const circle = document.createElementNS('http://www.w3.org/2000/svg', 'circle');
      circle.setAttribute('cx', station.x);
      circle.setAttribute('cy', station.y);
      circle.setAttribute('r', station.transfer ? 8 : 6);
      circle.setAttribute('class', station.transfer ? 'transfer-dot-5' : 'station-dot-5');
      svg.appendChild(circle);

      const text = document.createElementNS('http://www.w3.org/2000/svg', 'text');
      text.textContent = station.name;

      if (station.name === '那洪立交站') {
        // 【那洪立交文字调整】修改这里：
        text.setAttribute('x', station.x - 35);    // ← 文字 x 调整（左偏移）
        text.setAttribute('y', station.y - 8);     // ← 文字 y 调整
        text.setAttribute('text-anchor', 'end');    // ← 右对齐终点
      } else {
        text.setAttribute('x', station.x - 30);
        text.setAttribute('y', station.y + 5);
        text.setAttribute('text-anchor', 'end');
      }
      text.setAttribute('class', 'station-name');
      svg.appendChild(text);

      circle.addEventListener('click', () => {
        popup.innerHTML = `<div class="popup-title">${station.name}</div><div class="popup-transfer">5号线${station.transfer ? '（换乘站）' : ''}</div>`;
        popup.style.left = `${station.x + 20}px`;
        popup.style.top = `${station.y - 30}px`;
        popup.style.display = 'block';
        setTimeout(() => popup.style.display = 'none', 3000);
      });
    });

    // 5号线 - 那罗向左45度斜线延伸至那丹（90px）
    const path5Diagonal = document.createElementNS('http://www.w3.org/2000/svg', 'path');
    path5Diagonal.setAttribute('d', 'M850,800 L786,864');
    path5Diagonal.setAttribute('class', 'line-path-5');
    svg.appendChild(path5Diagonal);

    // 5号线标签（那丹终点站，左侧，避开文字）
    const labelBg5End = document.createElementNS('http://www.w3.org/2000/svg', 'rect');
    labelBg5End.setAttribute('x', 710);
    labelBg5End.setAttribute('y', 835);
    labelBg5End.setAttribute('width', 22);
    labelBg5End.setAttribute('height', 22);
    labelBg5End.setAttribute('rx', 11);
    labelBg5End.setAttribute('fill', '#3366CC');
    svg.appendChild(labelBg5End);

    const labelText5End = document.createElementNS('http://www.w3.org/2000/svg', 'text');
    labelText5End.setAttribute('x', 722);
    labelText5End.setAttribute('y', 848);
    labelText5End.setAttribute('class', 'line-label');
    labelText5End.textContent = '5';
    svg.appendChild(labelText5End);

    // ========== 机场线（#3FCED6）==========
    // 机场线标签（那丹站，右侧，避开文字）- 矩形标签
    const labelBgAirport = document.createElementNS('http://www.w3.org/2000/svg', 'rect');
    labelBgAirport.setAttribute('x', 804);
    labelBgAirport.setAttribute('y', 870);
    labelBgAirport.setAttribute('width', 36);
    labelBgAirport.setAttribute('height', 22);
    labelBgAirport.setAttribute('rx', 4);
    labelBgAirport.setAttribute('fill', '#3FCED6');
    svg.appendChild(labelBgAirport);

    const labelTextAirport = document.createElementNS('http://www.w3.org/2000/svg', 'text');
    labelTextAirport.setAttribute('x', 822);
    labelTextAirport.setAttribute('y', 885);
    labelTextAirport.setAttribute('class', 'line-label');
    labelTextAirport.textContent = '机场';
    svg.appendChild(labelTextAirport);

    // 机场线 - 那丹向左下延伸至新营房，再水平左延至吴圩机场
    const pathAirport = document.createElementNS('http://www.w3.org/2000/svg', 'path');
    pathAirport.setAttribute('d', 'M786,864 L609,1041 L409,1041');
    pathAirport.setAttribute('class', 'line-path-6');
    svg.appendChild(pathAirport);

    // 机场线 - 那丹站（换乘点，由机场线绘制圆点和文字）
    const nadanCircle = document.createElementNS('http://www.w3.org/2000/svg', 'circle');
    nadanCircle.setAttribute('cx', 786);
    nadanCircle.setAttribute('cy', 864);
    nadanCircle.setAttribute('r', 8);
    nadanCircle.setAttribute('class', 'transfer-dot-6');
    svg.appendChild(nadanCircle);

    const nadanText = document.createElementNS('http://www.w3.org/2000/svg', 'text');
    nadanText.setAttribute('x', 756);           // ← 文字 x 调整
    nadanText.setAttribute('y', 869);            // ← 文字 y 调整
    nadanText.setAttribute('text-anchor', 'end'); // ← 对齐方式
    nadanText.setAttribute('class', 'station-name');
    nadanText.textContent = '那丹';
    svg.appendChild(nadanText);

    nadanCircle.addEventListener('click', () => {
      popup.innerHTML = `<div class="popup-title">那丹</div><div class="popup-transfer">机场线 / 5号线（换乘站）</div>`;
      popup.style.left = '806px';
      popup.style.top = '834px';
      popup.style.display = 'block';
      setTimeout(() => popup.style.display = 'none', 3000);
    });

    // 机场线 - 新营房站（中点）
    const xinyingfangCircle = document.createElementNS('http://www.w3.org/2000/svg', 'circle');
    xinyingfangCircle.setAttribute('cx', 698);
    xinyingfangCircle.setAttribute('cy', 953);
    xinyingfangCircle.setAttribute('r', 6);
    xinyingfangCircle.setAttribute('class', 'station-dot-6');
    svg.appendChild(xinyingfangCircle);

    const xinyingfangText = document.createElementNS('http://www.w3.org/2000/svg', 'text');
    xinyingfangText.setAttribute('x', 668);           // ← 文字 x 调整
    xinyingfangText.setAttribute('y', 958);            // ← 文字 y 调整
    xinyingfangText.setAttribute('text-anchor', 'end'); // ← 对齐方式
    xinyingfangText.setAttribute('class', 'station-name');
    xinyingfangText.textContent = '新营房站';
    svg.appendChild(xinyingfangText);

    xinyingfangCircle.addEventListener('click', () => {
      popup.innerHTML = `<div class="popup-title">新营房站</div><div class="popup-transfer">机场线</div>`;
      popup.style.left = '718px';
      popup.style.top = '923px';
      popup.style.display = 'block';
      setTimeout(() => popup.style.display = 'none', 3000);
    });

    // 机场线 - 吴圩镇站（水平段中点）
    const wuxuzhenCircle = document.createElementNS('http://www.w3.org/2000/svg', 'circle');
    wuxuzhenCircle.setAttribute('cx', 509);
    wuxuzhenCircle.setAttribute('cy', 1041);
    wuxuzhenCircle.setAttribute('r', 6);
    wuxuzhenCircle.setAttribute('class', 'station-dot-6');
    svg.appendChild(wuxuzhenCircle);

    const wuxuzhenText = document.createElementNS('http://www.w3.org/2000/svg', 'text');
    wuxuzhenText.setAttribute('x', 509);
    wuxuzhenText.setAttribute('y', 1026);
    wuxuzhenText.setAttribute('text-anchor', 'middle');
    wuxuzhenText.setAttribute('class', 'station-name');
    wuxuzhenText.textContent = '吴圩镇站';
    svg.appendChild(wuxuzhenText);

    wuxuzhenCircle.addEventListener('click', () => {
      popup.innerHTML = `<div class="popup-title">吴圩镇站</div><div class="popup-transfer">机场线</div>`;
      popup.style.left = '529px';
      popup.style.top = '1011px';
      popup.style.display = 'block';
      setTimeout(() => popup.style.display = 'none', 3000);
    });

    // 机场线 - 吴圩机场站（终点站，文字加粗加大标黑）
    const wuxuAirportCircle = document.createElementNS('http://www.w3.org/2000/svg', 'circle');
    wuxuAirportCircle.setAttribute('cx', 409);
    wuxuAirportCircle.setAttribute('cy', 1041);
    wuxuAirportCircle.setAttribute('r', 8);
    wuxuAirportCircle.setAttribute('class', 'transfer-dot-6');
    svg.appendChild(wuxuAirportCircle);

    const wuxuAirportText = document.createElementNS('http://www.w3.org/2000/svg', 'text');
    wuxuAirportText.setAttribute('x', 409);
    wuxuAirportText.setAttribute('y', 1024);
    wuxuAirportText.setAttribute('text-anchor', 'middle');
    wuxuAirportText.setAttribute('font-size', '15');
    wuxuAirportText.setAttribute('font-weight', 'bold');
    wuxuAirportText.setAttribute('fill', '#000');
    wuxuAirportText.textContent = '吴圩机场';
    svg.appendChild(wuxuAirportText);

    wuxuAirportCircle.addEventListener('click', () => {
      popup.innerHTML = `<div class="popup-title">吴圩机场</div><div class="popup-transfer">机场线（终点站）</div>`;
      popup.style.left = '429px';
      popup.style.top = '1011px';
      popup.style.display = 'block';
      setTimeout(() => popup.style.display = 'none', 3000);
    });

    // 吴圩机场终点标签（矩形）
    const labelBgAirportEnd = document.createElementNS('http://www.w3.org/2000/svg', 'rect');
    labelBgAirportEnd.setAttribute('x', 395);
    labelBgAirportEnd.setAttribute('y', 1052);
    labelBgAirportEnd.setAttribute('width', 28);
    labelBgAirportEnd.setAttribute('height', 22);
    labelBgAirportEnd.setAttribute('rx', 4);
    labelBgAirportEnd.setAttribute('fill', '#3FCED6');
    svg.appendChild(labelBgAirportEnd);

    const labelTextAirportEnd = document.createElementNS('http://www.w3.org/2000/svg', 'text');
    labelTextAirportEnd.setAttribute('x', 409);
    labelTextAirportEnd.setAttribute('y', 1067);
    labelTextAirportEnd.setAttribute('class', 'line-label');
    labelTextAirportEnd.textContent = '机场';
    svg.appendChild(labelTextAirportEnd);

    // ========== 6号线（#FF9D01）- 三津→五一立交→青秀路 ==========
    const path6 = document.createElementNS('http://www.w3.org/2000/svg', 'path');
    path6.setAttribute('d', 'M370,275 L2170,275 L2437,190 L2980,190');
    path6.setAttribute('class', 'line-path-7');
    svg.appendChild(path6);

    const line6Stations = [
      { name: '三津', x: 370, y: 275 },
      { name: '同乐', x: 490, y: 275 },
      { name: '定秋坡', x: 610, y: 275 },
      { name: '横岭', x: 730, y: 275 },
      { name: '五一立交', x: 850, y: 275, transfer: true },
      { name: '新屋', x: 900, y: 275 },
      { name: '市二医院', x: 950, y: 275 },
      { name: '福建园站', x: 1000, y: 275, transfer: true },
      { name: '区人民医院', x: 1150, y: 275 },
      { name: '唐城路', x: 1300, y: 275 },
      { name: '医科大一附院', x: 1450, y: 275 },
      { name: '三中青山校区', x: 1600, y: 275 },
      { name: '埌西', x: 1750, y: 275, transfer: true },
      { name: '会展中心南', x: 1870, y: 275 },
      { name: '青秀路', x: 1990, y: 275 },
      { name: '合作路', x: 2110, y: 275 },
      { name: '市科技馆', x: 2304, y: 233 },
      { name: '琅东客运站', x: 2520, y: 190, transfer: true },
      { name: '林里桥', x: 2675, y: 190 },
      { name: '中医仙葫院区', x: 2830, y: 190 },
      { name: '天池山', x: 2980, y: 190 },
    ];

    line6Stations.forEach((station, index) => {
      const circle = document.createElementNS('http://www.w3.org/2000/svg', 'circle');
      circle.setAttribute('cx', station.x);
      circle.setAttribute('cy', station.y);
      circle.setAttribute('r', station.transfer ? 8 : 6);

      // 琅东客运站：1/6号线合并换乘点（胶囊样式，横跨两线）
      if (station.name === '琅东客运站') {
        // 外层阴影
        const shadow = document.createElementNS('http://www.w3.org/2000/svg', 'rect');
        shadow.setAttribute('x', 2494);
        shadow.setAttribute('y', 166);
        shadow.setAttribute('width', 32);
        shadow.setAttribute('height', 28);
        shadow.setAttribute('rx', 14);
        shadow.setAttribute('ry', 14);
        shadow.setAttribute('fill', 'rgba(0,0,0,0.15)');
        svg.appendChild(shadow);

        // 白色胶囊底
        const capsule = document.createElementNS('http://www.w3.org/2000/svg', 'rect');
        capsule.setAttribute('x', 2495);
        capsule.setAttribute('y', 165);
        capsule.setAttribute('width', 30);
        capsule.setAttribute('height', 26);
        capsule.setAttribute('rx', 13);
        capsule.setAttribute('ry', 13);
        capsule.setAttribute('fill', '#fff');
        capsule.setAttribute('stroke', '#bbb');
        capsule.setAttribute('stroke-width', 1.5);
        svg.appendChild(capsule);

        // 内部1号线绿色小圆（上）
        const dot1 = document.createElementNS('http://www.w3.org/2000/svg', 'circle');
        dot1.setAttribute('cx', 2510);
        dot1.setAttribute('cy', 173);
        dot1.setAttribute('r', 4.5);
        dot1.setAttribute('fill', '#00A651');
        svg.appendChild(dot1);

        // 内部6号线橙色小圆（下）
        const dot6 = document.createElementNS('http://www.w3.org/2000/svg', 'circle');
        dot6.setAttribute('cx', 2510);
        dot6.setAttribute('cy', 183);
        dot6.setAttribute('r', 4.5);
        dot6.setAttribute('fill', '#FF9D01');
        svg.appendChild(dot6);
      } else {
        circle.setAttribute('class', station.transfer ? 'transfer-dot-7' : 'station-dot-7');
        svg.appendChild(circle);
      }

      const text = document.createElementNS('http://www.w3.org/2000/svg', 'text');
      text.textContent = station.name + (station.name === '五一立交' || station.name === '福建园站' || station.name === '埌西' || station.name === '琅东客运站' ? '' : '站');
      if (station.name === '五一立交') {
        text.setAttribute('x', station.x - 35);
        text.setAttribute('y', station.y + 25);
        text.setAttribute('text-anchor', 'end');
      } else if (station.name === '福建园站') {
        text.setAttribute('x', station.x + 32);
        text.setAttribute('y', station.y + 20);
        text.setAttribute('text-anchor', 'middle');
      } else if (station.name === '埌西') {
        text.setAttribute('x', station.x + 22);
        text.setAttribute('y', station.y + 24);
        text.setAttribute('text-anchor', 'middle');
      } else if (station.name === '琅东客运站') {
        text.setAttribute('x', station.x + 22);
        text.setAttribute('y', station.y + 18);
        text.setAttribute('text-anchor', 'start');
      } else {
        // 文字上下交错
        const isEven = index % 2 === 0;
        text.setAttribute('x', station.x);
        text.setAttribute('y', isEven ? station.y - 14 : station.y + 24);
        text.setAttribute('text-anchor', 'middle');
      }
      text.setAttribute('class', 'station-name');
      svg.appendChild(text);

      circle.addEventListener('click', () => {
        const transferInfo = station.name === '五一立交' ? ' / 5号线（换乘站）' :
                             station.name === '福建园站' ? ' / 2号线（换乘站）' :
                             station.name === '埌西' ? ' / 3号线（换乘站）' :
                             station.name === '琅东客运站' ? ' / 1号线（换乘站）' : '';
        popup.innerHTML = `<div class="popup-title">${station.name}${station.name === '五一立交' || station.name === '福建园站' || station.name === '埌西' || station.name === '琅东客运站' ? '' : '站'}</div><div class="popup-transfer">6号线${transferInfo}</div>`;
        popup.style.left = `${station.x + 20}px`;
        popup.style.top = `${station.y - 40}px`;
        popup.style.display = 'block';
        setTimeout(() => popup.style.display = 'none', 3000);
      });
    });

    // 6号线标签（三津起始站）
    const labelBg6 = document.createElementNS('http://www.w3.org/2000/svg', 'rect');
    labelBg6.setAttribute('x', 340);
    labelBg6.setAttribute('y', 261);
    labelBg6.setAttribute('width', 22);
    labelBg6.setAttribute('height', 22);
    labelBg6.setAttribute('rx', 11);
    labelBg6.setAttribute('fill', '#FF9D01');
    svg.appendChild(labelBg6);

    const labelText6 = document.createElementNS('http://www.w3.org/2000/svg', 'text');
    labelText6.setAttribute('x', 351);
    labelText6.setAttribute('y', 274);
    labelText6.setAttribute('class', 'line-label');
    labelText6.textContent = '6';
    svg.appendChild(labelText6);

    // 天池山终点标签
    const labelBg6End = document.createElementNS('http://www.w3.org/2000/svg', 'rect');
    labelBg6End.setAttribute('x', 2994);
    labelBg6End.setAttribute('y', 176);
    labelBg6End.setAttribute('width', 22);
    labelBg6End.setAttribute('height', 22);
    labelBg6End.setAttribute('rx', 11);
    labelBg6End.setAttribute('fill', '#FF9D01');
    svg.appendChild(labelBg6End);

    const labelText6End = document.createElementNS('http://www.w3.org/2000/svg', 'text');
    labelText6End.setAttribute('x', 3005);
    labelText6End.setAttribute('y', 189);
    labelText6End.setAttribute('class', 'line-label');
    labelText6End.textContent = '6';
    svg.appendChild(labelText6End);

    // 明秀路站圆点（由5号线绘制）
    const mingxiuCircle = document.createElementNS('http://www.w3.org/2000/svg', 'circle');
    mingxiuCircle.setAttribute('cx', 1000);
    mingxiuCircle.setAttribute('cy', 20);
    mingxiuCircle.setAttribute('r', 8);
    mingxiuCircle.setAttribute('class', 'transfer-dot-5');
    svg.appendChild(mingxiuCircle);

    // 明秀路站文字（向右偏移 30px）
    const mingxiuText = document.createElementNS('http://www.w3.org/2000/svg', 'text');
    mingxiuText.setAttribute('x', 970);
    mingxiuText.setAttribute('y', 38);
    mingxiuText.setAttribute('text-anchor', 'start');
    mingxiuText.setAttribute('class', 'station-name');
    mingxiuText.textContent = '明秀路站';
    svg.appendChild(mingxiuText);

    mingxiuCircle.addEventListener('click', () => {
      popup.innerHTML = `<div class="popup-title">明秀路站</div><div class="popup-transfer">5号线（换乘站）</div>`;
      popup.style.left = '1020px';
      popup.style.top = '-10px';
      popup.style.display = 'block';
      setTimeout(() => popup.style.display = 'none', 3000);
    });

    // 5号线 - 北湖南路站（明秀路支线上）
    const beihuCircle = document.createElementNS('http://www.w3.org/2000/svg', 'circle');
    beihuCircle.setAttribute('cx', 1050);
    beihuCircle.setAttribute('cy', 20);
    beihuCircle.setAttribute('r', 6);
    beihuCircle.setAttribute('class', 'station-dot-5');
    svg.appendChild(beihuCircle);

    const beihuText = document.createElementNS('http://www.w3.org/2000/svg', 'text');
    beihuText.setAttribute('x', 1050);
    beihuText.setAttribute('y', 02);
    beihuText.setAttribute('text-anchor', 'middle');
    beihuText.setAttribute('class', 'station-name');
    beihuText.textContent = '北湖南路';
    svg.appendChild(beihuText);

    beihuCircle.addEventListener('click', () => {
      popup.innerHTML = `<div class="popup-title">北湖南路</div><div class="popup-transfer">5号线</div>`;
      popup.style.left = '1070px';
      popup.style.top = '-10px';
      popup.style.display = 'block';
      setTimeout(() => popup.style.display = 'none', 3000);
    });

    // 5号线 - 虎邱站（连接线 1/3 处）
    const huqiuCircle = document.createElementNS('http://www.w3.org/2000/svg', 'circle');
    huqiuCircle.setAttribute('cx', 1283);
    huqiuCircle.setAttribute('cy', -37);
    huqiuCircle.setAttribute('r', 6);
    huqiuCircle.setAttribute('class', 'station-dot-5');
    svg.appendChild(huqiuCircle);

    const huqiuText = document.createElementNS('http://www.w3.org/2000/svg', 'text');
    huqiuText.setAttribute('x', 1283);
    huqiuText.setAttribute('y', -13);
    huqiuText.setAttribute('text-anchor', 'middle');
    huqiuText.setAttribute('class', 'station-name');
    huqiuText.textContent = '虎邱';
    svg.appendChild(huqiuText);

    huqiuCircle.addEventListener('click', () => {
      popup.innerHTML = `<div class="popup-title">虎邱</div><div class="popup-transfer">5号线</div>`;
      popup.style.left = '1303px';
      popup.style.top = '-67px';
      popup.style.display = 'block';
      setTimeout(() => popup.style.display = 'none', 3000);
    });

    // 5号线 - 狮山公园站（连接线 2/3 处）
    const shishanCircle = document.createElementNS('http://www.w3.org/2000/svg', 'circle');
    shishanCircle.setAttribute('cx', 1467);
    shishanCircle.setAttribute('cy', -93);
    shishanCircle.setAttribute('r', 6);
    shishanCircle.setAttribute('class', 'station-dot-5');
    svg.appendChild(shishanCircle);

    const shishanText = document.createElementNS('http://www.w3.org/2000/svg', 'text');
    shishanText.setAttribute('x', 1467);
    shishanText.setAttribute('y', -69);
    shishanText.setAttribute('text-anchor', 'middle');
    shishanText.setAttribute('class', 'station-name');
    shishanText.textContent = '狮山公园';
    svg.appendChild(shishanText);

    shishanCircle.addEventListener('click', () => {
      popup.innerHTML = `<div class="popup-title">狮山公园</div><div class="popup-transfer">5号线</div>`;
      popup.style.left = '1487px';
      popup.style.top = '-123px';
      popup.style.display = 'block';
      setTimeout(() => popup.style.display = 'none', 3000);
    });

    // ========== 线路图例（吴圩机场附近空白处） ==========
    const legendX = 30;
    const legendY = 920;
    const lineHeight = 44;

    // 标题：线路 LINES
    const titleText1 = document.createElementNS('http://www.w3.org/2000/svg', 'text');
    titleText1.setAttribute('x', legendX);
    titleText1.setAttribute('y', legendY);
    titleText1.setAttribute('font-size', '24');
    titleText1.setAttribute('font-weight', 'bold');
    titleText1.setAttribute('fill', '#D4A017');
    titleText1.textContent = '线路';
    svg.appendChild(titleText1);

    const titleText2 = document.createElementNS('http://www.w3.org/2000/svg', 'text');
    titleText2.setAttribute('x', legendX + 58);
    titleText2.setAttribute('y', legendY);
    titleText2.setAttribute('font-size', '20');
    titleText2.setAttribute('fill', '#E8D47C');
    titleText2.textContent = 'LINES';
    svg.appendChild(titleText2);

    // 分隔线
    const sepLine = document.createElementNS('http://www.w3.org/2000/svg', 'line');
    sepLine.setAttribute('x1', legendX);
    sepLine.setAttribute('y1', legendY + 12);
    sepLine.setAttribute('x2', legendX + 200);
    sepLine.setAttribute('y2', legendY + 12);
    sepLine.setAttribute('stroke', '#D4A017');
    sepLine.setAttribute('stroke-width', '1.5');
    svg.appendChild(sepLine);

    // 线路数据
    const legendLines = [
      { color: '#00A651', cn: '1 号线', en: 'Line 1' },
      { color: '#E53935', cn: '2 号线', en: 'Line 2' },
      { color: '#9966CC', cn: '3 号线', en: 'Line 3' },
      { color: '#C4CC23', cn: '4 号线', en: 'Line 4' },
      { color: '#3366CC', cn: '5 号线', en: 'Line 5' },
      { color: '#FF9D01', cn: '6 号线', en: 'Line 6' },
      { color: '#3FCED6', cn: '机场线', en: 'Airport Line' },
    ];

    legendLines.forEach((item, i) => {
      const y = legendY + 30 + i * lineHeight;

      // 颜色条
      const bar = document.createElementNS('http://www.w3.org/2000/svg', 'rect');
      bar.setAttribute('x', legendX);
      bar.setAttribute('y', y - 12);
      bar.setAttribute('width', 28);
      bar.setAttribute('height', 14);
      bar.setAttribute('rx', '3');
      bar.setAttribute('fill', item.color);
      svg.appendChild(bar);

      // 中文名称
      const cnText = document.createElementNS('http://www.w3.org/2000/svg', 'text');
      cnText.setAttribute('x', legendX + 42);
      cnText.setAttribute('y', y);
      cnText.setAttribute('font-size', '16');
      cnText.setAttribute('fill', '#555');
      cnText.textContent = item.cn;
      svg.appendChild(cnText);

      // 英文名称
      const enText = document.createElementNS('http://www.w3.org/2000/svg', 'text');
      enText.setAttribute('x', legendX + 100);
      enText.setAttribute('y', y);
      enText.setAttribute('font-size', '11');
      enText.setAttribute('fill', '#999');
      enText.textContent = item.en;
      svg.appendChild(enText);
    });

    // ========== 缩放和拖拽功能 ==========
    let scale = 1;
    let translateX = 0;
    let translateY = 0;
    let isDragging = false;
    let startX, startY;

    function updateTransform() {
      svg.style.transformOrigin = 'center center';
      svg.style.transform = `translate(${translateX}px, ${translateY}px) scale(${scale})`;
    }

    // 鼠标滚轮缩放
    container.addEventListener('wheel', (e) => {
      e.preventDefault();
      const delta = e.deltaY > 0 ? 0.95 : 1.05;
      const newScale = Math.min(Math.max(scale * delta, 0.3), 5);
      scale = newScale;
      updateTransform();
    }, { passive: false });

    // 触摸缩放（双指捏合）
    let lastTouchDist = null;
    container.addEventListener('touchmove', (e) => {
      if (e.touches.length === 2) {
        e.preventDefault();
        const dx = e.touches[0].clientX - e.touches[1].clientX;
        const dy = e.touches[0].clientY - e.touches[1].clientY;
        const dist = Math.sqrt(dx * dx + dy * dy);
        if (lastTouchDist !== null) {
          const delta = dist / lastTouchDist;
          scale = Math.min(Math.max(scale * delta, 0.3), 5);
          updateTransform();
        }
        lastTouchDist = dist;
      } else if (e.touches.length === 1 && isDragging) {
        const dx = e.touches[0].clientX - startX;
        const dy = e.touches[0].clientY - startY;
        translateX += dx;
        translateY += dy;
        startX = e.touches[0].clientX;
        startY = e.touches[0].clientY;
        updateTransform();
      }
    }, { passive: false });

    container.addEventListener('touchstart', (e) => {
      if (e.touches.length === 2) {
        const dx = e.touches[0].clientX - e.touches[1].clientX;
        const dy = e.touches[0].clientY - e.touches[1].clientY;
        lastTouchDist = Math.sqrt(dx * dx + dy * dy);
      } else if (e.touches.length === 1) {
        isDragging = true;
        startX = e.touches[0].clientX;
        startY = e.touches[0].clientY;
      }
    });

    container.addEventListener('touchend', () => {
      lastTouchDist = null;
      isDragging = false;
    });

    // 鼠标拖拽
    container.addEventListener('mousedown', (e) => {
      isDragging = true;
      startX = e.clientX;
      startY = e.clientY;
    });
    window.addEventListener('mousemove', (e) => {
      if (!isDragging) return;
      const dx = e.clientX - startX;
      const dy = e.clientY - startY;
      translateX += dx;
      translateY += dy;
      startX = e.clientX;
      startY = e.clientY;
      updateTransform();
    });
    window.addEventListener('mouseup', () => isDragging = false);

    // 双击重置
    container.addEventListener('dblclick', () => {
      scale = 1;
      translateX = 0;
      translateY = 0;
      updateTransform();
    });
  </script>
</body>
</html>

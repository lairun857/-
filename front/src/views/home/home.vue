<template>
<div :style="{ height: clientHeight }" class="tech-dashboard">
    <!-- Header -->
    <div class="dashboard-header">
        <div class="header-left">
            <span class="tech-icon-spin"><Icon type="ios-aperture" size="30" color="#00f3ff"/></span>
            <span class="system-title">ASSET COMMAND CENTER</span>
        </div>
        <div class="header-right">
            <div class="status-pod">
                <span class="label">USER:</span>
                <span class="value">{{ name }}</span>
            </div>
             <div class="status-pod">
                <span class="label">LOC:</span>
                <span class="value">{{ location }}</span>
            </div>
            <div class="status-pod time-pod">
                <span class="value glow-text">{{ showtime }} {{ showtime2 }}</span>
            </div>
        </div>
    </div>

    <!-- Main Content Grid (3 Columns) -->
    <div class="dashboard-grid">
        
        <!-- Left Column (25%): Navigation/Modules -->
        <div class="grid-section left-panel">
            <div class="section-title">CORE MODULES</div>
            <div class="module-grid">
                <div class="tech-card module-card" v-for="(item, index) in addMenuTempList" :key="index" @click="selectItem(item)">
                    <div class="card-corner"></div>
                    <div class="module-content">
                        <span class="module-text">{{ item.title }}</span>
                        <div class="module-status">ONLINE</div>
                    </div>
                </div>
            </div>
            
            <div class="section-title" style="margin-top: 20px;">QUICK ACCESS</div>
            <div class="threeButton">
                 <div class="tech-btn-action" @click="toDaiBanPage">
                     <span class="btn-icon"><Icon type="md-time" /></span>
                     <span class="btn-text">TASKS</span>
                 </div>
                 <div class="tech-btn-action" @click="toFaQiPage">
                     <span class="btn-icon"><Icon type="md-flag" /></span>
                     <span class="btn-text">PENDING</span>
                 </div>
                 <div class="tech-btn-action" @click="toJingBanPage">
                     <span class="btn-icon"><Icon type="md-checkmark-circle-outline" /></span>
                     <span class="btn-text">HISTORY</span>
                 </div>
            </div>

            <div class="bottomText" @click="toOwnMenu">
                <span class="link-text">>> CONFIGURE DASHBOARD <<</span>
            </div>
        </div>

        <!-- Center Column (50%): Tactical Map / Charts / Stats -->
        <div class="grid-section center-panel">
            <div class="section-title">SYSTEM ANALYTICS</div>
            
            <!-- Big Stats Row -->
            <div class="stats-row">
                <div class="stat-box">
                    <div class="stat-label">TOTAL ASSETS</div>
                    <div class="stat-value counter-text">{{ totalAssets }}</div>
                    <div class="stat-bar"><div class="stat-bar-fill" style="width: 75%"></div></div>
                </div>
                <div class="stat-box">
                    <div class="stat-label">PERSONNEL</div>
                    <div class="stat-value counter-text" style="color: #7000ff">{{ totalPersonnel }}</div>
                    <div class="stat-bar"><div class="stat-bar-fill purple-fill" style="width: 60%"></div></div>
                </div>
                 <div class="stat-box">
                    <div class="stat-label">ALERTS</div>
                    <div class="stat-value counter-text" style="color: #ff0055">3</div>
                    <div class="stat-bar"><div class="stat-bar-fill red-fill" style="width: 10%"></div></div>
                </div>
            </div>

            <!-- Chart Container -->
            <div class="chart-container-wrapper">
                <div class="chart-title">ASSET DISTRIBUTION</div>
                 <div id="ring-chart-container" class="chart-box"></div>
                 <div class="chart-overlay"></div>
            </div>

            <div class="center-bottom-msg">
                SYSTEM STATUS: <span style="color: #0f0; margin-left:10px;">OPTIMAL</span>
            </div>
        </div>

        <!-- Right Column (25%): Dynamic Data Streams -->
        <div class="grid-section right-panel">
            <!-- Stream 1: Asset Activity -->
            <div class="stream-container">
                <div class="stream-header">
                     <span class="blink-dot"></span>
                     LIVE ASSET DATA STREAM
                </div>
                <div class="stream-window" @click="$router.push('/adminAsset')">
                    <div class="scroll-content">
                        <div class="data-row" v-for="(item, i) in mockAssetData" :key="'asset-'+i">
                            <span class="time">[{{item.time}}]</span>
                            <span class="type">{{item.type}}</span>
                            <span class="msg">{{item.message}}</span>
                        </div>
                         <!-- Duplicate for seamless scroll effect -->
                        <div class="data-row" v-for="(item, i) in mockAssetData" :key="'asset-dup-'+i">
                            <span class="time">[{{item.time}}]</span>
                            <span class="type">{{item.type}}</span>
                            <span class="msg">{{item.message}}</span>
                        </div>
                    </div>
                </div>
            </div>

            <!-- Stream 2: Active Users -->
            <div class="stream-container">
                <div class="stream-header">
                     <span class="blink-dot user-dot"></span>
                     ACTIVE PERSONNEL MONITOR
                </div>
                 <div class="stream-window" @click="$router.push('/user')">
                    <div class="scroll-content reverse-scroll">
                         <div class="user-row" v-for="(user, i) in mockUserData" :key="'user-'+i">
                            <div class="user-avatar"></div>
                            <div class="user-info">
                                <div class="u-name">{{user.name}}</div>
                                <div class="u-dept">{{user.dept}}</div>
                            </div>
                            <div class="u-status">ACTIVE</div>
                        </div>
                        <!-- Duplicate -->
                        <div class="user-row" v-for="(user, i) in mockUserData" :key="'user-dup-'+i">
                            <div class="user-avatar"></div>
                            <div class="user-info">
                                <div class="u-name">{{user.name}}</div>
                                <div class="u-dept">{{user.dept}}</div>
                            </div>
                            <div class="u-status">ACTIVE</div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>
</template>

<script>
import Cookies from "js-cookie";
import { Pie } from '@antv/g2plot';
import {
    ipInfo,
    getMyDoorList6
} from "./api.js";

export default {
    name: "home",
    data() {
        return {
            name: "",
            showtime: "",
            showtime2: "",
            location: "Network Intranet",
            addMenuTempList: [],
            clientHeight: '100%',
            
            // Mock Data for "Tech" effect
            totalAssets: 1245,
            totalPersonnel: 382,
            
            mockAssetData: [
                { time: '10:01', type: '[OUT]', message: 'MacBook Pro #A001 assigned to Employee A' },
                { time: '10:05', type: '[IN]', message: 'Projector #P203 returned to Warehouse' },
                { time: '10:12', type: '[MAINT]', message: 'Server Rack #S05 requires updates' },
                { time: '10:15', type: '[NEW]', message: 'Asset #Z999 registered in system' },
                { time: '10:20', type: '[AUDIT]', message: 'Monthly audit completed for Dept B' },
                { time: '10:30', type: '[WARN]', message: 'Asset #L302 warranty near expiry' }
            ],
            mockUserData: [
                { name: 'Admin', dept: 'System Ops' },
                { name: 'User_001', dept: 'Finance' },
                { name: 'User_002', dept: 'HR' },
                { name: 'User_003', dept: 'IT Support' },
                { name: 'User_004', dept: 'Dev Team' },
                { name: 'User_005', dept: 'Marketing' }
            ]
        };
    },

    methods: {
        init() {
            this.getMyDoorListFx(); // Retrieve real menu data
            let user = JSON.parse(Cookies.get("userInfo"));
            this.name = user.nickname;
            this.getNowTime();
            ipInfo().then((res) => {
                if (res.success) {
                    this.location = res.result;
                }
            });
            this.timer = setInterval(this.getNowTime, 1000);
        },
        selectItem(e) {
            if (e.name != undefined && e.name != "null") {
                this.$router.push({
                    name: e.name,
                });
            }
        },
        toDaiBanPage() {
            this.$Message.success("Module Initializing...");
        },
        toFaQiPage() {
             this.$Message.success("Module Initializing...");
        },
        toJingBanPage() {
             this.$Message.success("Module Initializing...");
        },
        toOwnMenu() {
            this.$router.push("/myHome");
        },
        getMyDoorListFx() {
            var that = this;
            getMyDoorList6().then((res) => {
                that.addMenuTempList = res.result;
            });
        },
        getNowTime() {
            this.showtime = this.format(new Date(), "yyyy-MM-dd");
            this.showtime2 = this.format(new Date(), "HH:mm:ss");
        },
        format(date, fmt) {
            let o = {
                "M+": date.getMonth() + 1,
                "d+": date.getDate(),
                "H+": date.getHours(),
                "m+": date.getMinutes(),
                "s+": date.getSeconds(),
                "q+": Math.floor((date.getMonth() + 3) / 3),
                "S": date.getMilliseconds()
            };
            if (/(y+)/.test(fmt))
                fmt = fmt.replace(RegExp.$1, (date.getFullYear() + "").substr(4 - RegExp.$1.length));
            for (let k in o)
                if (new RegExp("(" + k + ")").test(fmt))
                    fmt = fmt.replace(RegExp.$1, (RegExp.$1.length == 1) ? (o[k]) : (("00" + o[k]).substr(("" + o[k]).length)));
            return fmt;
        },
        initChart() {
            const data = [
                { type: 'Computers', value: 450 },
                { type: 'Furniture', value: 300 },
                { type: 'Vehicles', value: 50 },
                { type: 'Network', value: 200 },
                { type: 'Others', value: 245 },
            ];

            const piePlot = new Pie('ring-chart-container', {
                appendPadding: 10,
                data,
                angleField: 'value',
                colorField: 'type',
                radius: 0.8,
                innerRadius: 0.64,
                label: {
                    type: 'inner',
                    offset: '-50%',
                    content: '{value}',
                    style: {
                        textAlign: 'center',
                        fontSize: 14,
                        fill: '#fff',
                    },
                },
                statistic: {
                    title: false,
                    content: {
                        style: {
                            whiteSpace: 'pre-wrap',
                            overflow: 'hidden',
                            textOverflow: 'ellipsis',
                            color: '#00f3ff',
                            fontSize: '20px'
                        },
                        content: 'ASSETS',
                    },
                },
                theme: 'dark',
                color: ['#00f3ff', '#7000ff', '#ff0055', '#00ffaa', '#ffe600'],
                legend: {
                    position: 'bottom',
                    itemName: {
                        style: { fill: '#e0e0ff' }
                    }
                }
            });

            piePlot.render();
        }
    },
    mounted() {
        this.init();
        this.clientHeight = `${document.documentElement.clientHeight}px`;
        let that = this;
        window.onresize = function () {
            that.clientHeight = `${document.documentElement.clientHeight}px`;
        };
        // Initialize Chart after brief delay to ensure DOM is ready
        setTimeout(() => {
            this.initChart();
        }, 500);
    },
};
</script>

<style lang="less" scoped>
/* Tech Theme Variables */
@tech-bg: #050510;
@tech-primary: #00f3ff;
@tech-secondary: #7000ff;
@tech-surface: rgba(20, 20, 35, 0.6);
@tech-border: rgba(0, 243, 255, 0.3);
@tech-text: #e0e0ff;

.tech-dashboard {
    background-color: @tech-bg;
    color: @tech-text;
    font-family: 'Orbitron', 'Microsoft YaHei', sans-serif;
    overflow: hidden;
    height: 100vh;
    background-image: 
        radial-gradient(circle at 10% 20%, rgba(112, 0, 255, 0.1) 0%, transparent 20%),
        radial-gradient(circle at 90% 80%, rgba(0, 243, 255, 0.1) 0%, transparent 20%),
        linear-gradient(rgba(0,0,0,0.2) 1px, transparent 1px),
        linear-gradient(90deg, rgba(0,0,0,0.2) 1px, transparent 1px);
    background-size: 100% 100%, 100% 100%, 30px 30px, 30px 30px;
}

.dashboard-header {
    height: 80px;
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 0 40px;
    border-bottom: 1px solid @tech-border;
    background: rgba(0,0,0,0.5);
    backdrop-filter: blur(5px);
}

.header-left {
    display: flex;
    align-items: center;
}

.tech-icon-spin {
    animation: icon-spin 10s linear infinite;
    margin-right: 15px;
}
@keyframes icon-spin { 
    0% { transform: rotate(0deg); }
    100% { transform: rotate(360deg); } 
}

.system-title {
    font-size: 24px;
    font-weight: bold;
    color: @tech-primary;
    text-shadow: 0 0 10px @tech-primary;
    letter-spacing: 2px;
}

.header-right {
    display: flex;
    gap: 20px;
}

.status-pod {
    background: rgba(0, 243, 255, 0.1);
    border: 1px solid @tech-border;
    padding: 8px 15px;
    border-radius: 4px;
    font-size: 14px;
    
    .label {
        color: rgba(255,255,255,0.5);
        margin-right: 8px;
    }
    .value {
        color: #fff;
        font-weight: bold;
    }
}

.time-pod {
    border-color: @tech-secondary;
    background: rgba(112, 0, 255, 0.1);
}

.glow-text {
    text-shadow: 0 0 5px @tech-secondary;
}

.dashboard-grid {
    display: flex;
    height: calc(100% - 80px);
    padding: 30px;
    gap: 30px;
}

.grid-section {
    background: @tech-surface;
    border: 1px solid @tech-border;
    border-radius: 10px;
    padding: 20px;
    box-shadow: 0 0 20px rgba(0, 0, 0, 0.5);
    backdrop-filter: blur(5px);
    display: flex;
    flex-direction: column;
}

/* Layout Columns */
.left-panel { width: 25%; }
.center-panel { width: 50%; position: relative; }
.right-panel { width: 25%; }

.section-title {
    font-size: 18px;
    color: @tech-primary;
    border-bottom: 2px solid @tech-primary;
    padding-bottom: 10px;
    margin-bottom: 20px;
    display: inline-block;
    width: fit-content;
    text-shadow: 0 0 5px @tech-primary;
}

/* Modules (Left) */
.module-grid {
    display: flex;
    flex-wrap: wrap;
    gap: 20px;
    flex: 1;
    align-content: flex-start;
    overflow-y: auto;
}

.tech-card {
    width: 45%; 
    min-width: 140px;
    height: 100px;
    background: rgba(255,255,255,0.05);
    border: 1px solid rgba(255,255,255,0.1);
    position: relative;
    cursor: pointer;
    transition: all 0.3s;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    
    &:hover {
        background: rgba(0, 243, 255, 0.1);
        border-color: @tech-primary;
        box-shadow: 0 0 15px rgba(0, 243, 255, 0.2);
        .card-corner { background: @tech-primary; }
        .module-text { color: #fff; }
    }
}

.card-corner {
    position: absolute;
    top: 0; right: 0;
    width: 10px; height: 10px;
    background: rgba(255,255,255,0.2);
    transition: all 0.3s;
}

.module-text {
    font-size: 16px;
    color: rgba(255,255,255,0.8);
    margin-bottom: 5px;
    text-align: center;
}

.module-status {
    font-size: 10px;
    color: @tech-primary;
    letter-spacing: 1px;
}

.threeButton {
    display: flex;
    justify-content: space-between;
    height: 80px;
    margin-bottom: 20px;
}

.tech-btn-action {
    flex: 1;
    margin: 0 5px;
    background: linear-gradient(135deg, rgba(0, 243, 255, 0.1), transparent);
    border: 1px solid @tech-border;
    display: flex;
    justify-content: center;
    align-items: center;
    cursor: pointer;
    transition: all 0.3s;
    border-radius: 4px;
    flex-direction: column;
    
    &:hover {
        transform: translateY(-2px);
        background: rgba(0, 243, 255, 0.2);
        box-shadow: 0 5px 15px rgba(0, 0, 0, 0.3);
    }
}

.btn-icon {
    font-size: 20px;
    color: @tech-primary;
    margin-bottom: 5px;
}

.btn-text {
    font-size: 12px;
    font-weight: bold;
}

.bottomText {
    margin-top: 10px;
    text-align: center;
    cursor: pointer;
    
    .link-text {
        font-size: 14px;
        color: rgba(255,255,255,0.5);
        transition: all 0.3s;
        letter-spacing: 1px;
    }
    &:hover .link-text {
        color: @tech-primary;
        text-shadow: 0 0 5px @tech-primary;
    }
}

/* Center Panel Stats & Chart */
.stats-row {
    display: flex;
    justify-content: space-between;
    margin-bottom: 30px;
}

.stat-box {
    flex: 1;
    margin: 0 10px;
    background: rgba(0,0,0,0.3);
    padding: 15px;
    border-radius: 8px;
    text-align: center;
    border: 1px solid rgba(255,255,255,0.05);
}

.stat-label {
    font-size: 12px;
    color: rgba(255,255,255,0.5);
    letter-spacing: 1px;
    margin-bottom: 5px;
}

.stat-value {
    font-size: 32px;
    color: @tech-primary;
    font-weight: bold;
    font-family: 'Courier New', monospace;
    text-shadow: 0 0 10px rgba(0,0,0,0.5);
}

.stat-bar {
    width: 100%;
    height: 4px;
    background: rgba(255,255,255,0.1);
    margin-top: 10px;
    border-radius: 2px;
}

.stat-bar-fill {
    height: 100%;
    background: @tech-primary;
    box-shadow: 0 0 5px @tech-primary;
    border-radius: 2px;
}
.purple-fill { background: @tech-secondary; box-shadow: 0 0 5px @tech-secondary; }
.red-fill { background: #ff0055; box-shadow: 0 0 5px #ff0055; }

.chart-container-wrapper {
    flex: 1;
    position: relative;
    background: rgba(0,0,0,0.2);
    border-radius: 10px;
    padding: 20px;
    display: flex;
    flex-direction: column;
}

.chart-title {
    text-align: center;
    color: rgba(255,255,255,0.8);
    font-size: 14px;
    margin-bottom: 20px;
    letter-spacing: 2px;
}

.chart-box {
    flex: 1;
    min-height: 300px;
}

.chart-overlay {
    position: absolute;
    top: 0; left: 0; right: 0; bottom: 0;
    background: radial-gradient(circle, transparent 50%, rgba(0,0,0,0.2) 100%);
    pointer-events: none;
    z-index: 1;
}

.center-bottom-msg {
    margin-top: 20px;
    text-align: center;
    font-size: 14px;
    color: rgba(255,255,255,0.5);
    border-top: 1px solid rgba(255,255,255,0.1);
    padding-top: 15px;
}

/* Right Panel Streams */
.stream-container {
    flex: 1;
    display: flex;
    flex-direction: column;
    margin-bottom: 20px;
}
.stream-container:last-child { margin-bottom: 0; }

.stream-header {
    background: rgba(0,0,0,0.3);
    padding: 10px;
    font-size: 14px;
    font-weight: bold;
    display: flex;
    align-items: center;
    border-bottom: 1px solid rgba(255,255,255,0.1);
}

.blink-dot {
    width: 8px; height: 8px;
    background: #00ff00;
    border-radius: 50%;
    margin-right: 10px;
    animation: blink 1s infinite;
}
.user-dot { background: @tech-secondary; }

@keyframes blink { 50% { opacity: 0.3; } }

.stream-window {
    flex: 1;
    background: rgba(0,0,0,0.2);
    overflow: hidden;
    position: relative;
    cursor: pointer;
    
    &:hover {
        background: rgba(0,0,0,0.3);
    }
}

.scroll-content {
    animation: scrollUp 20s linear infinite; // Slower scroll
    padding: 10px;
}

.reverse-scroll {
    animation: scrollUp 25s linear infinite;
}

@keyframes scrollUp {
    0% { transform: translateY(0); }
    100% { transform: translateY(-50%); } 
}

/* Data Rows */
.data-row {
    padding: 8px 0;
    border-bottom: 1px dashed rgba(255,255,255,0.1);
    font-family: 'Courier New', monospace;
    font-size: 12px;
    display: flex;
}

.data-row .time { color: @tech-primary; margin-right: 5px; white-space: nowrap; }
.data-row .type { color: #f0f; margin-right: 5px; font-weight: bold; white-space: nowrap; }
.data-row .msg { color: #ccc; overflow: hidden; text-overflow: ellipsis; white-space: nowrap; }

.user-row {
    display: flex;
    align-items: center;
    padding: 10px 5px;
    border-bottom: 1px solid rgba(255,255,255,0.05);
}

.user-avatar {
    width: 30px; height: 30px;
    background: linear-gradient(45deg, @tech-secondary, #00f);
    border-radius: 50%;
    margin-right: 10px;
}

.user-info {
    flex: 1;
    .u-name { color: #fff; font-size: 14px; }
    .u-dept { color: rgba(255,255,255,0.5); font-size: 12px; }
}

.u-status {
    font-size: 10px;
    color: #0f0;
    border: 1px solid #0f0;
    padding: 2px 5px;
    border-radius: 2px;
}
</style>

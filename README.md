<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>M-dimensional Volume Incremental Rewritten</title>
    <link href="./assets/style/core.css" rel="stylesheet">

    
    <script src="./assets/script/loader.js" defer></script>
    <script src="./assets/script/technical/vue.min.js" defer></script>
    <script src="./assets/script/technical/ExpantaNum.js" defer></script>
    <script src="./assets/script/technical/format_expantanum.js" defer></script>
    <script src="./assets/script/technical/hotkeys.js" defer></script>
    <script src="./assets/script/formatTime.js" defer></script>
    <script src="./assets/script/display.js" defer></script>
    <script src="./assets/script/constants.js" defer></script>
    <script src="./assets/script/temp.js" defer></script>
    <script src="./assets/script/script.js" defer></script>
    <script src="./assets/script/newsticker.js" defer></script>
    <script src="./assets/script/save.js" defer></script>
    <script src="./assets/script/components.js" defer></script>
    <script src="./assets/script/changelog.js" defer></script>
    <script src="./assets/script/offline.js" defer></script>
    <script src="./assets/script/dimboost.js" defer></script>
    <script src="./assets/script/hotkeys.js" defer></script>

    <script src="./assets/script/mm3.js" defer></script>
    <script src="./assets/script/automation.js" defer></script>

    <script src="./assets/script/PL2/mm5.js" defer></script>

</head>

<body>
    <div id="app">
        <div>
            <newsticker></newsticker>
            <div class="text-center">
                你有 <span class="vol-dis">{{formatWhole(player.volumes)}}</span> mm<sup>4</sup> 4维体积
            </div>
            <div v-if="player.timeSpeed != 1" class="text-center">当前全局游戏速度: ×{{format(player.timeSpeed)}}</div>
            <div v-if="player.volumes.gte('1e200') && !player.isPL1unlocked" class="text-center">你需要???才能走得更远！

            </div>
            <div v-if="hasMM3Upg(4)" v-html="displayChallenge()" class="text-center"></div>
            <div v-if="player.volumes.gte(mm3ChallengeGoal[player.PL1inchal]) && !player.PL1breakedPL1limit" class="text-center">
                <button class="btn mm3btn" @click="doMM3resetManmade()">重置1-8维度，4维体积，维度提升，获得{{format(tmp.mm3.gain)}} mm<sup>3</sup> 3维体积</button>
            </div>
            <div v-if="player.isPL1unlocked" class="mm3text text-center">你有{{format(player.PL1points)}} mm<sup>3</sup> 3维体积</div>
            <div v-if="player.isPL2unlocked" class="mm5text text-center">你有{{format(player.PL2points)}} mm<sup>5</sup> 5维体积</div>
            <div v-if="showAllPrestigeLayers">
                <div class="mm6text text-center">你有0.00 mm<sup>6</sup> 6维体积</div>
                <div class="mm7text text-center">你有0.00 mm<sup>7</sup> 7维体积</div>
                <div class="mm8text text-center">你有0.00 mm<sup>8</sup> 8维体积</div>
                <div class="mm9text text-center">你有0.00 mm<sup>9</sup> 9维体积</div>
                <div class="mm10text text-center">你有0.00 mm<sup>10</sup> 10维体积</div>
                
            </div>
            <div v-if="isEndgame()" :style="{color: getUndulatingColor()}" class="text-center">已达到当前版本终点！</div>
        </div>
        <div class="main-line"></div>
        <div class="text-center">
            <button v-for="tab in primaryTabSort()" :class="getTabClass(tab.name)" :style="tab.style" v-html="tab.text"
                :disabled="tabShow.inPrimaryTab(tab.name)" @click="player.currentPage = tab.id">

            </button>
        </div>
        <div class="main-line"></div>
        <div class="text-center">
            <button v-for="tab in secondaryTabSort()" :class="getSubTabClass(tab.parentTab,tab.name)" :style="tab.style"
                v-html="tab.text" :disabled="tabShow.inSecondaryTab(tab.parentTab,tab.name)"
                @click="player.currentPage = tab.id">

            </button>
        </div>
        <div class="main-line"></div>
        <div v-if="player.currentPage == 10">
            <div class="text-center" v-if="!player.PL1xiaopengyouUnl"><button class="btn mm3btn" @click="unlockXiaopengyou()">
                解锁小朋友<br>
                需要1.000e6 mm<sup>3</sup>
            </button></div>
            <div v-else>
                <div class="text-center">
                    <p>你有<span class="mm3text">{{format(player.PL1xiaopengyouPoints)}}(+{{format(getXiaopengyouGain())}}/s)</span>小朋友，他们使每次购买的乘数×{{format(xiaopengyouEffect1())}}</p>
                    <p>已解锁一个新的购买项在"升级"选项卡</p>
                </div>
                <p class="mm3text text-center">
                    在1000小朋友，基于小朋友数量获得额外的维度提升<br>
                    当前：+{{format(xiaopengyouEffect2())}}
                </p>
            </div>
        </div>
        <div v-if="player.currentPage == 7 && hasMM3Upg(4)">
            <p class="text-center">下列是挑战，在进入挑战时会进行一次无奖励的重置，<br>如果在这个挑战里达到了目标，则为通过这个挑战。</p>
            <p class="text-center"><button class="btn mm3btn" @click="exitChal()">退出挑战</button></p>
            <table>
                <tr>
                    <td><button class="chalpl0" @click="enterNorChal(1)" :class="getChalClass(1)">挑战1<br>1-8维度的增长速度为原来的^2<br>目标: 1.000e200 mm<sup>4</sup><br>奖励：获得×1.02多的购买次数</button></td>
                    <td><button class="chalpl0" @click="enterNorChal(2)" :class="getChalClass(2)" v-if="hasMM3Upg(14)">挑战2<br>维度提升实际数量为原来的0.1倍<br>目标: 1.000e700 mm<sup>4</sup><br>奖励：小朋友再次提升维度提升实际数量</button></td>
                    
                </tr>
            </table>
        </div>
        <div v-if="player.currentPage == 9" class="text-center">
            <button class="btn mm3btn" @click="doMM3resetManmade()">重置1-8维度，4维体积，维度提升，获得{{format(tmp.mm3.gain)}} mm<sup>3</sup> 3维体积<br>需要 1.000e200 mm<sup>4</sup></button>
            <button class="btn mm5btn" @click="doMM5resetManmade()" v-if="hasMM3Upg(20)">重置4维和3维的所有资源和升级，获得{{format(tmp.mm5.gain)}} mm<sup>5</sup> 5维体积<br>需要 1e100,000 mm<sup>4</sup></button>
            
        </div>
        <div v-if="player.currentPage == 8" class="text-center">
            <div v-html="getAutomationTabDetail()"></div>
            <button class="btn" v-if="hasMM3Upg(7)" @click="toggleAutobuyer(10)">
                自动mm<sup>3</sup>重置: {{player.auto.includes(10) ? "开" :
                "关"}}
            </button>
        </div>
        <div v-if="player.currentPage == 6" class="text-center">
            <table>
                <tr>
                    <td><button class="mm3upg" :class="{unlocked: hasMM3Upg(1)}" @click="buyMM3Upg(1)">基于重置数量提升1-8维度，维度提升价格/1.000e10<br>花费：1 mm<sup>3</sup></button></td>
                    <td><button class="mm3upg" :class="{unlocked: hasMM3Upg(2)}" @click="buyMM3Upg(2)">解锁自动<br>花费：1 mm<sup>3</sup></button></td>
                    <td><button class="mm3upg" :class="{unlocked: hasMM3Upg(3)}" @click="buyMM3Upg(3)">解锁自动维度提升<br>花费：3 mm<sup>3</sup></button></td>
                    <td><button class="mm3upg" :class="{unlocked: hasMM3Upg(4)}" @click="buyMM3Upg(4)">解锁挑战<br>花费：10 mm<sup>3</sup></button></td>
                </tr>
                <tr>
                    <td><button class="mm3upg" :class="{unlocked: hasMM3Upg(5)}" @click="buyMM3Upg(5)">你可以最大化维度提升<br>花费：15 mm<sup>3</sup></button></td>
                    <td><button class="mm3upg" :class="{unlocked: hasMM3Upg(6)}" @click="buyMM3Upg(6)">每次重置开始于10维度提升<br>花费：25 mm<sup>3</sup></button></td>
                    <td><button class="mm3upg" :class="{unlocked: hasMM3Upg(7)}" @click="buyMM3Upg(7)">每次重置开始于35维度提升，解锁自动mm<sup>3</sup>重置<br>花费：40 mm<sup>3</sup></button></td>
                    <td><button class="mm3upg" :class="{unlocked: hasMM3Upg(8)}" @click="buyMM3Upg(8)">解锁一个双倍mm<sup>3</sup>获取的可购买<br>花费：60 mm<sup>3</sup></button></td>
                </tr>
                <tr>
                    <td><button class="mm3upg" :class="{unlocked: hasMM3Upg(9)}" @click="buyMM3Upg(9)">维度提升不重置任何东西<br>花费：95 mm<sup>3</sup></button></td>
               </tr>
                <tr v-if="player.PL1xiaopengyouUnl">
                    <td>
                        <button class="mm3upg" :class="{unlocked: hasMM3Upg(13)}" @click="buyMM3Upg(13)">在"维度提升"页面解锁"维度提升^2"<br>花费：2,000 小朋友</button>
                    </td>
                    <td>
                        <button class="mm3upg" :class="{unlocked: hasMM3Upg(14)}" @click="buyMM3Upg(14)">解锁挑战2<br>花费：10,000 小朋友</button>
                    </td>
                    <td>
                        <button class="mm3upg" :class="{unlocked: hasMM3Upg(15)}" @click="buyMM3Upg(15)">基于小朋友数量提升小朋友获取<br>花费：50,000 小朋友</button>
                    </td>
                    <td>
                        <button class="mm3upg" :class="{unlocked: hasMM3Upg(16)}" @click="buyMM3Upg(16)">基于小朋友数量减少维度提升的价格<br>花费：300,000 小朋友</button>
                    </td>
                </tr>
                <tr v-if="hasMM3Upg(16)">
                    
                    <td>
                        <button class="mm3upg" :class="{unlocked: hasMM3Upg(17)}" @click="buyMM3Upg(17)">小朋友加成维度提升23的效果<br>花费：700,000 小朋友</button>
                    </td>
                    <td>
                        <button class="mm3upg" :class="{unlocked: hasMM3Upg(18)}" @click="buyMM3Upg(18)">基于4维体积加成小朋友获取<br>花费：1.000e6 小朋友</button>
                    </td>
                    <td>
                        <button class="mm3upg" :class="{unlocked: hasMM3Upg(19)}" @click="buyMM3Upg(19)">基于3维体积加成小朋友获取<br>花费：2.000e6 小朋友</button>
                    </td>
                    <td>
                        <button class="mm3upg" :class="{unlocked: hasMM3Upg(20)}" @click="buyMM3Upg(20)">解锁下一个声望层（建议开128倍达成）<br>花费：2.000e11 小朋友</button>
                    </td>
                </tr>
                <tr>
                    <td><button class="mm3upg" style="border: var(--color-text-invert)"></button></td>
                </tr>
                <tr>
                    <td><button class="mm3upg" style="border: var(--color-text-invert)"></button></td>
                    <td v-if="hasMM3Upg(8)"><button class="mm3upg" @click="buyBuyable(1)">双倍mm<sup>3</sup>获取({{ formatWhole(player.PL1buyable1) }})<br>花费：{{format(getBuyableCost(1))}} mm<sup>3</sup><br>效果: ×{{format(getBuyableEffect(1))}}</button></td>
                    <td v-else><button class="mm3upg" style="border: var(--color-text-invert)"></button></td>
                    <td v-if="player.PL1xiaopengyouUnl"><button class="mm3upg" @click="buyBuyable(2)">
                        双倍小朋友获取({{ formatWhole(player.PL1buyable2) }})<br>花费：{{format(getBuyableCost(2))}} 小朋友<br>效果: ×{{format(getBuyableEffect(2))}}</button>
                    </td>
                    <td v-else><button class="mm3upg" style="border: var(--color-text-invert)"></button></td>

                    
                    <td><button class="mm3upg" style="border: var(--color-text-invert)"></button></td>
                </tr>
            </table>

        </div>
        <div v-if="player.currentPage == 1" class="text-center">
            <button class="btn" @click="buyall()" @touchstart="isHoldMax = true;" @touchend="isHoldMax = false;"
                v-on:mousedown="isHoldMax = true;" v-on:mouseup.native="isHoldMax = false;">
                购买最大
            </button>
            <br>
            <p>每次购买乘数: {{format(tmp.dimension.getBoughtMultiplier())}}</p>
            <div v-for="dim in dimensions" :key="dim.id" class="text-center">
                {{ dim.label }}
                +<span>{{
                    format(player.dimensions[DIMENSIONS_POINTS][dim.id
                    - 1]) }}
                </span>
                x<span>{{
                    format(player.dimensions[DIMENSIONS_MULTI][dim.id
                    - 1]) }}
                </span>
                ^<span>{{
                    format(player.dimensions[DIMENSIONS_EXPONENT][dim.id
                    - 1]) }}
                </span>
                <span>{{
                    format(player.dimensions[DIMENSIONS_BOUGHT][dim.id
                    - 1]) }}
                </span>
                <button class="btn" :disabled="!buyable(dim.id)" @click="buydim(dim.id)" v-html="display4DDimCost(dim.id)">价格:
                    {{format(player.dimensions[DIMENSIONS_COST][dim.id - 1 ])}} mm<sup>4</sup>
                </button>
                <!-- <button class="btn" :disabled="!buyable(dim.id)" @click="buydim(dim.id)">
                        Cost: <span v-html="format(player.dimensions[DIMENSIONS_COST][dim.id
                            - 1])"></span>
                    </button>-->
                    <button class="btn" v-if="hasMM3Upg(2)" @click="toggleAutobuyer(dim.id)">
                        自动: {{player.auto.includes(dim.id) ? "开" :
                        "关"}}
                    </button>
            </div>
        </div>

        <div v-if="player.currentPage == 2" class="text-center">
            <button class="btn" @click="formattedHardReset">硬重置</button>
            <button class="btn" @click="exportCopy">导出</button>
            <button class="btn" @click="exportFile">导出为文件</button>
            <!-- <button class="btn" @click="importSave">导入</button> -->
            <button class="btn" @click="importFile">以文件导入</button>
        </div>
        <div v-if="player.currentPage == 3" class="text-center">
            <div>
                <span v-html="formatEndgame()"></span><br />
                当前版本： {{changelog[0].version}}<br />
                使用
                <a href="https://github.com/Naruyoko/ExpantaNum.js" target="_blank">Naruyoko/ExpantaNum.js</a>
                <a href="https://github.com/cloudytheconqueror/letter-notation-format"
                    target="_blank">cloudytheconqueror/letter-notation-format</a>
                处理大数字<br />

                <hr>
                <p>
                    支持所有序数增量吧吧友，点击下面玩吧友们制作的游戏
                </p>
                <a href="https://seanxlx2011.github.io/" target="_blank" class="game-link">
                    Incremental Data
                    Rewritten</a>,<br />
                <a href="https://0i00000000a7.github.io/points-incremental-rewritten/" target="_blank"
                    class="game-link">
                    Points Incremental Rewritten
                </a>,<br />
                <a href="https://aster131072.github.io/incremental_evolution/" target="_blank"
                    class="game-link">Incremental
                    Evolution</a>,<br />
                <a href="https://dlsdl.github.io/wind_spirit_creation/" target="_blank" class="game-link">Wind
                    spirit
                    creation</a>,<br />
                <a href="https://qqqe308.github.io/The-Rhythm-Game-Tree/" target="_blank" class="game-link">The Rhythm
                    Game
                    Tree
                </a>,<br />
                <a href="https://a262537412640768744.github.io/homework-incremental/main.html" target="_blank"
                    class="game-link">
                    Homework Incremental </a>,<br />
                <a href="https://goldenapple125.github.io/RBN/" target="_blank" class="game-link">The Road of
                    Big Number </a>,<br />
                <a href="https://qqqe308.github.io/Anti-Anti-Softcap-Tree/111/" target="_blank" class="game-link">
                    Anti anti softcap tree </a>,<br />
                <br />
                <br />
                <p>你也可以玩其他的增量游戏！</p>
                <a href="https://ivark.github.io/AntimatterDimensions" target="_blank" class="game-link">Antimatter
                    Dimensions</a>,<br />
                <a href="https://seanxlx2011.github.io/" target="_blank" class="game-link">
                    <del>Infinity Dimension Rewritten</del> </a>,<br />
                <a href="https://jacorb90.github.io/Prestige-Tree/" target="_blank" class="game-link">
                    The Prestige Tree </a>,<br />
                <a href="https://patcailmemer.github.io/Ordinal-Markup" target="_blank" class="game-link tooltipBox">
                    Ordinal Markup<span class="tooltip">Stop updated 2021年3月23日</span> </a><br />
                <hr />
                <h1>---更新日志---</h1>
                <changelog></changelog>
            </div>
        </div>
        <div v-if="player.currentPage == 4" class="text-center">
            <div>
                <p>维度提升({{format(player.dimBoost)}},{{format(getRealDimBoost())}})</p>
                <button class="btn" @click="dimBoost" v-html="dimBoostDescription()"></button><br>
                <button class="btn" v-if="hasMM3Upg(3)" @click="toggleAutobuyer(9)">
                    自动: {{player.auto.includes(9) ? "开" :
                    "关"}}
                </button>
            </div>
            <div v-if="hasMM3Upg(13)">
                <p>维度提升^2({{format(player.dimBoost2)}})</p>
                <button class="btn" @click="dimBoost2" v-html="dimBoost2Description()">
                </button>
            </div>
            <div v-html="statBoosts()"></div>

        </div>
        <div v-if="player.currentPage == 5" class="text-center">
            <p style="color: #aaa">你离线了{{formatTime.fromSeconds(player.offlinedTime)}}</p>
            <p>拉动下面的进度条可以改变游戏速度，但需要消耗离线时间</p>
            <p><button class="btn" @click="switchGameState">切换游戏状态<br>当前：{{player.isOffline ? "停止" : "未停止"}}</button>
            </p>
            <input type="range" min="0" max="7" step="0.001" style="width: 300px"
                v-model.number="player.offlinePower"><br>

        </div>
        <div v-if="player.currentPage == 11870903" class="text-center">
            <br>
            <br>
            <button style="
            background: #eeeeee;
            animation: a-tesseract-shift-dark 5s infinite;
            max-width: 150px;
            color: #000;
            cursor: pointer;
            ">You have enough Infinity Points to buy a Tesseract</button><br><br><br>
            <div  style="
            animation: bigcrunch 2s infinite;">啊，我被大嘎吱嘎吱了，功力只剩一成</div>
        </div>
        <div v-if="player.currentPage == 11" class="text-center">
            20XX年，世界末日到来，<br>
            邪恶博士XXX(代号:O(/əʊ/))宣称自己掌握了可以毁灭宇宙的技术，同时也放出了<br>
            一张被挑战书，被挑战书（破损）的内容如下：<br>
            <div style="width: 500px; height: 707.1067811865476px; border: #fff solid 2px; margin: auto;">
                <h1 style="font-size: 1cm">被挑战书</h1>
                <p style="text-indent: 2em; text-align: left;">
                    我乃<br><br><br><br>若你欲成挑战者，请联系130(/lɪŋ/)<br><br><br><br><br>&nbsp;&nbsp;&nbsp;&nbsp;不要贸然挑战我，否则后果自负<br><br><br><br>
                </p>
                <p style="text-align: right;">XXX&nbsp;<br>20XX年3月1日</p>
            </div>
            在被挑战书放出后几年，O(/əʊ/)组织有一人叛变，向【数据删除】报告了被挑战书的完整内容，<br>也报告了挑战地方的位置。<br>
            在进入了挑战位置后，在挑战房间角落找到了一张"前人"留下的纸条：<div class="backgroundsettingnote" @click="enterFinalChallenge()">
                不仅只有这一个房间，&nbsp;&nbsp;还有无数个&nbsp;&nbsp;房间...<br><br>
                还有最终挑&nbsp;&nbsp;战...K...一&nbsp;&nbsp;...E...十...四...

            </div><br>
        </div>
        <div v-if="player.currentPage == 11870502" class="text-center">
            <p>当你第一次达到break-eternity极限时，会发生某些意想不到的事...</p>
            计划目标<br>
            mm3before: 0<br>
            mm3: e200<br>
            mm5: e100000<br>
            mm6: e1e11<br>
            mm7: e1.7e723<br>
            mm8: ee9.25e9<br>
            mm9: ee1e8000<br>
            mm10: 1f7<br>
            mm11: f1.797e308<br>
        </div>
    </div>
</body>

</html>

<template xmlns:v-on="http://www.w3.org/1999/xhtml" xmlns:v-bind="http://www.w3.org/1999/xhtml">
    <div>
        <div class="col-md-3">
            <div style="padding-top:50%;margin-left: 5px;">
                <span style="
               font-family: SquareFont;
               color: white;
               font-size: 18px;">
                    来访记录
                </span>
            </div>
            <div class="row" v-for="item in historyList" style="margin-top: 5px;margin-left: 2px;">
                <div class="form-inline">
                    <img class="el-col-xs-12" v-if="item.photo!=null" :src="item.photo"
                         style="width: 60px;height: 60px; border-radius: 50%;margin-top: 5px;">
                    <div class="el-col-xs-12" style="color: white;font-size: 14px;margin-top: 26px;margin-left: 3px;">
                        {{item.name}}
                    </div>
                </div>
            </div>
        </div>
        <div id="container_vip"></div>
    </div>

</template>

<script>

    var rootWidth = window.innerWidth;
    var rootHeight = window.innerHeight;
    var stage = null;
    var layer = null;
    var dateLayer = null;
    var circle = null;
    var userImgSize = 90;
    var vipsignList = [];
    const MaxCount = 10;
    const HistoryMAXCOUNT = 6;

    $(document).ready(function () {
        loadData();
    })
    $(window).resize(function () {
        console.log("onresize")
    });

    function drawCurrentDayImage(currentDay) {
//添加中心圆-当前具体日期-天
        var currentDayImage = new Image();//Html Image
        currentDayImage.onload = function () {
            let dimg = new Konva.Image({
                image: this,
                x: Math.round(stage.getWidth() / 2 - 200 / 2) + 1,
                y: Math.round(stage.getHeight() / 2 - 200 / 2),
                width: 200,
                height: 200,
            });
            dimg.setName(`currentDayImage`);
            dimg.setId(`currentDayImage`);
            dateLayer.add(dimg);
            stage.add(dateLayer);
        };
        let currentDayImageName = `circle_date_${currentDay}.png`;
        if (currentDay.toString().length == 1) {
            currentDayImageName = `circle_date_0${currentDay}.png`;
        }
        currentDayImage.src = require('../assets/img/day/' + currentDayImageName);
    }

    function drawAllDaysImage(alldays) {
//添加中心圆-日期-天
        var dayImage = new Image();//Html Image
        dayImage.onload = function () {
            let dimg = new Konva.Image({
                image: this,
                x: Math.round(stage.getWidth() / 2 - 200 / 2) + 1,
                y: Math.round(stage.getHeight() / 2 - 200 / 2),
                width: 200,
                height: 200,
            });
            dimg.setName(`dayImage`);
            dimg.setId(`dayImage`);
            dateLayer.add(dimg);
        };
        let dayImageName = `circle_date_all_${alldays}.png`;
        dayImage.src = require('../assets/img/' + dayImageName);
    }

    function drawCurrentMonthImage(currentMonth) {
        var currentMonthImage = new Image();//Html Image
        currentMonthImage.onload = function () {
            let mimg = generateImage(this,
                {
                    x: stage.getWidth() / 2 - 160 / 2,
                    y: stage.getHeight() / 2 - 160 / 2,
                },
                {
                    w: 160,
                    h: 160
                });//Konva Image
            mimg.setName(`currentMonthImage`);
            mimg.setId(`currentMonthImage`);
            dateLayer.add(mimg);
        };
        let currentMonthImageName = `circl_month_${currentMonth}.png`;
        if (currentMonth.toString().length == 1) {
            currentMonthImageName = `circl_month_0${currentMonth}.png`;
        }
        currentMonthImage.src = require('../assets/img/month/' + currentMonthImageName);
    }

    function drawAllMonthImage() {
//添加中心圆-月份
        var monthImage = new Image();//Html Image
        monthImage.onload = function () {
            let mimg = generateImage(this,
                {
                    x: stage.getWidth() / 2 - 160 / 2,
                    y: stage.getHeight() / 2 - 160 / 2,
                },
                {
                    w: 160,
                    h: 160
                });//Konva Image
            mimg.setName(`monthImage`);
            mimg.setId(`monthImage`);
            dateLayer.add(mimg);
        };
        monthImage.src = require('../assets/img/circl_month_all.png');
    }

    function drawCircleImage() {
//添加中心圆
        var circleImage = new Image();//Html Image
        circleImage.onload = function () {
            let img = generateImage(this,
                {
                    x: stage.getWidth() / 2 - 180 / 2,
                    y: stage.getHeight() / 2 - 180 / 2,
                },
                {
                    w: 180,
                    h: 180
                });//Konva Image
            img.setName(`circleImage`);
            img.setId(`circleImage`);
            dateLayer.add(img);
            drawWelcome();
        };
        circleImage.src = require('../assets/img/bg_mid_round.png');
    }

    function drawWelcome() {
        var welcomeImage = new Image();//Html Image
        welcomeImage.onload = function () {
            let img = new Konva.Image({
                image: welcomeImage,
                x: stage.getWidth() / 2 - 40,
                y: stage.getHeight() / 2 - 25,
                width: 80,
                height: 35,
            });
            img.setName(`welcomeImage`);
            img.setId(`welcomeImage`);
            dateLayer.add(img);
            stage.draw();
        };
        welcomeImage.src = require('../assets/img/text_welcome.png');
    }

    function loadData() {

        if (layer && layer != null) {
            layer.clear();
        }

        if (dateLayer && dateLayer != null) {
            dateLayer.clear();
        }

        if (stage && stage != null) {
            stage.clear();
        }

        rootWidth = window.innerWidth;
        rootHeight = window.innerHeight;
        stage = new Konva.Stage({
            container: 'container_vip',
            width: rootWidth,
            height: rootHeight,

        });
        layer = new Konva.Layer();
        dateLayer = new Konva.Layer();
        let r = 60
        circle = new Konva.Circle({
            x: stage.getWidth() / 2,
            y: stage.getHeight() / 2,
            radius: r, //半径
            fill: '',
            stroke: 'white',
            strokeWidth: 2
        });
        dateLayer.add(circle);

        circle.hide();

        drawCircleImage();

        drawAllMonthImage();

        let date = new Date();
        let currentMonth = date.getMonth() + 1;//从 Date 对象返回月份 (0 ~ 11)
        let currentDay = date.getDate();
        let alldays = getDaysByMonth(date.getYear(), currentMonth);

        drawCurrentMonthImage(currentMonth);

        drawAllDaysImage(alldays);

        drawCurrentDayImage(currentDay);
    }

    /*
     * 添加每个部门对象到图层，包括文字，直线
     *
     */
    function generateEndPointer(index, startPointer) {
        let angle = 360 / MaxCount * index;
        let arrowLength = getRandomInt(rootWidth / 2 - circle.getRadius(), rootWidth / 2) - userImgSize / 2;//这里可以位置的调整取值范围
        let endPointer = getPointByAngle({x: 0, y: 1}, arrowLength * 0.5 - 100, angle);// 外圏结束点，角度不变，仅长度变长。需要考虑右边还有VIP及名字的显示+100

        if (index % 5 == 0) {
            arrowLength = getRandomInt(rootHeight / 2 - circle.getRadius(), rootHeight / 2) - userImgSize;
            endPointer = getPointByAngle({x: 0, y: 1}, arrowLength * 0.8, angle); //内圏
        }
        let slope = (endPointer.y - startPointer.y) / (endPointer.x - startPointer.x); //斜率

        if (Math.abs(endPointer.x) >= rootWidth / 2 - userImgSize / 2) {
            if (endPointer.x < 0) {
                endPointer.x += Math.abs(endPointer.x) - (rootWidth / 2 - userImgSize / 2);
            } else {
                endPointer.x -= Math.abs(endPointer.x) - (rootWidth / 2 - userImgSize / 2);
            }
            if (endPointer.x == startPointer.x) {
                endPointer.y -= userImgSize / 2;
            }
            else {
                endPointer.y = slope * endPointer.x;
            }
        }
        if (Math.abs(endPointer.y) >= rootHeight / 2 - userImgSize / 2) {
            if (endPointer.y < 0) {
                endPointer.y += Math.abs(endPointer.y) - (rootHeight / 2 - userImgSize);
            } else {
                endPointer.y -= Math.abs(endPointer.y) - (rootHeight / 2 - userImgSize);
            }
            endPointer.x = endPointer.y / slope;
        }
        return endPointer;
    }

    function addDepartmentToUI(item) {
        let index = item.index;
        let angle = 360 / MaxCount * index;// 算出每一个对象所要显示在圆周上的角度
        let startPointer = getPointByAngle({x: 0, y: 1}, 200 / 2, angle); //线起始点，随着角度变化 200/2为外圈的半径
        var endPointer = generateEndPointer(index, startPointer);

        var arrowLine = new Konva.Arrow({
            x: circle.getX(),//圆 心
            y: circle.getY(),//圆 心
            points: [startPointer.x, startPointer.y, endPointer.x, endPointer.y], //
            pointerLength: 0,//不显示箭头
            pointerWidth: 0,//不显示箭头
            stroke: '#DF6911',
            strokeWidth: 2,
            opacity: 0.5,
            id: `arrowLine_${index}`,
        });
        layer.add(arrowLine);


        var group = new Konva.Group({
            x: 0,
            y: 0,
            opacity: 0.5,
            id: `group_${index}`,
        });

        let circlePointer = getPointByAngle(endPointer, userImgSize / 2, angle);

        let imageObj = new Image();
        imageObj.src = item.photo;
        imageObj.onload = function () {

            let userCircle = new Konva.Circle({
                x: circle.getX() + circlePointer.x,
                y: circle.getY() + circlePointer.y,
                radius: userImgSize / 2, //半径
                fill: '',
                stroke: 'rgb(255,105,42)',
                strokeWidth: 4,
                id: `userCircle_${index}`,
                fillPatternOffset: {x: imageObj.width / 2, y: imageObj.height / 2},
                fillPatternRepeat: 'no-repeat',
                fillPatternScale: {
                    x: 90 / imageObj.width,
                    y: 90 / imageObj.height,
                }
            });
            userCircle.fillPatternImage(imageObj);

            var signedUser = new Konva.Text({
                x: circle.getX() + circlePointer.x - userCircle.getRadius() / 2 + 70,
                y: circle.getY() + circlePointer.y - userCircle.getRadius() / 2 + 30,
                text: item.name,//TODO
                fontSize: 12,
                fontFamily: 'SquareFont',
                fill: 'White',
                align: 'center',
                id: `signedUser_${index}`,
            });

            var vipImage = new Image();//Html Image
            vipImage.onload = function () {
                let img = new Konva.Image({
                    image: vipImage,
                    x: circle.getX() + circlePointer.x - userCircle.getRadius() / 2 + 70,
                    y: circle.getY() + circlePointer.y - userCircle.getRadius() / 2,
                    width: 35,
                    height: 14,
                    id: `vipImage_${index}`
                });
                group.add(img);
                stage.draw();
            };
            vipImage.src = require(`../assets/img/vip_tips.png`);
            group.add(signedUser);
            group.add(userCircle);
            layer.add(group);
            stage.add(layer);
            vipsignList.push(item);
            if (_this.historyList.length >= HistoryMAXCOUNT) {
                _this.historyList.splice(0, 1);
            }
            _this.historyList.push(item);
        };
    }

    /*
     *  生成图对象 Konva Image
     *  Note：这不是Html上 的image
     * */
    function generateImage(img, pointer, size) {
        if (!img) {
            return null;
        }
        return new Konva.Image({
            image: img,
            x: pointer.x,
            y: pointer.y,
            width: size.w,
            height: size.h,
        });
    }

    /*
     * 根据角度算出相应在圆周上的点坐标，后续要以这点为起始点画直线
     * */
    function getPointByAngle(centerPoint, r, angle) {
        let point = {};
        //从centerPoint点开始算 r时，过圆心上的圆周交点坐标x=r*sin(angle),y=r*cos(angle)
        point.x = Math.sin(angle * Math.PI / 180) * r + centerPoint.x;
        point.y = centerPoint.y - Math.cos(angle * Math.PI / 180) * r;
        return point;
    }

    var currentInterval = 0;
    var currentFakeDataInterval = 0;
    var _this

    function loadSessionData() {
        var sessionList = sessionStorage.getItem("sessionList");
        try {
            if (sessionList && sessionList.length > 0) {
                sessionList = JSON.parse(sessionList);
                if (sessionList && sessionList.length > 0) {
                    for (let item of sessionList) {
                        item.updateTime = new Date();
                        console.log(`loadSessionData index:${item.index}`)
                        for (let vipItem of vipsignList) {
                            if (item.index == vipItem.index) {//已经存在了，避免重合
                                item.index = _this.getAvailableIndex();
                                console.log(`loadSessionData new index:${item.index}`)
                            }
                        }
                        addDepartmentToUI(item);
                    }
                }
            }
        } catch (e) {
            console.log(e);
        }
    }

    export default {
        name: "VipSignPage",
        components: {},
        data() {
            _this = this;
            return {
                isLoading: false,
                historyList: [],
            }
        },
        methods: {
            reloadData() {
                setTimeout(() => {
                    loadData()
                    loadSessionData();
                }, 200)
            },

            updateData(dataList) {
                _this.isLoading = true;
                if (!dataList || dataList.length == 0) {
                    return;
                }
                if (!stage) {
                    sleep(300);
                }

                if (vipsignList.length >= MaxCount) {
                    _this.playAnimationToReset(vipsignList[0]);
                    vipsignList.splice(0, 1);
                }
                //console.log(`[vipsign] updateData dataList:\r\n${JSON.stringify(dataList)}`);
                for (let i = 0; i < 1; i++) {
                    _this.updateDataToUI(dataList[i], dataList, (itemList) => {

                        _this.isLoading = false;
                        if (!itemList || itemList.length == 0) {
                            return;
                        }
                        _this.updateData(itemList);
                    });
                    break
                }
            },

            updateDataToUI(data, dataList, callback) {
                let tid = setTimeout(() => {
                    var index = _this.getAvailableIndex();
                    let itemData = Object.assign(data, {
                        updateTime: new Date(),
                        index: index,
                    });
                    addDepartmentToUI(itemData);

                    setTimeout(() => {
                        _this.playZoominAnimation(itemData);
                    }, 50)
                    dataList.splice(0, 1);
                    callback(dataList);
                    window.clearTimeout(tid);
                }, 100)
            },

            getAvailableIndex: function () {
                let index = -1;
                if (vipsignList.length == 0) {
                    index = 0;
                }
                else {
                    for (let i = 0; i < MaxCount; i++) {
                        let isFound = false;
                        for (let item of vipsignList) {
                            if (item.index == i) {
                                isFound = true;
                                break;
                            }
                        }
                        if (!isFound) {
                            index = i;
                            break;
                        }
                    }
                }
                if (index == -1) {
                    if (vipsignList.length > 0) {
                        index = vipsignList[0].index;
                        _this.playAnimationToReset(vipsignList[0]);
                        vipsignList.splice(0, 1);
                        sleep(100);
                    }
                }
                return index;
            },

            updateDateImage() {
                let date = new Date();
                let m = date.getMonth() + 1;//从 Date 对象返回月份 (0 ~ 11)
                let d = date.getDate();
                let alldays = getDaysByMonth(date.getYear(), m);
                _this.updateAllDaysImage(alldays);
                _this.updateCurrentMonthImage(m);
                _this.updateCurrentDayImage(d);
            },

            updateAllDaysImage(alldays) {
                let dayImage = stage.find('#dayImage')[0];
                if (dayImage) {
                    dayImage.remove();
                    dayImage.destroy();
                    dayImage = null;
                }
                drawAllDaysImage(alldays);
            },

            updateCurrentMonthImage(currentMonth) {
                let currentMonthImage = stage.find('#currentMonthImage')[0];
                if (currentMonthImage) {
                    currentMonthImage.remove();
                    currentMonthImage.destroy();
                    currentMonthImage = null;
                }
                drawCurrentMonthImage(currentMonth);
            },

            updateCurrentDayImage(currentDay) {
                let currentDayImage = stage.find('#currentDayImage')[0];
                if (currentDayImage) {
                    currentDayImage.remove();
                    currentDayImage.destroy();
                    currentDayImage = null;
                }
                drawCurrentDayImage(currentDay);
            },

            //有人刷卡，数据更新了，将执行动画
            playZoominAnimation(item) {
                try {
                    let index = item.index;
                    let rate = 0.35;

                    let arrowLine = stage.find(`#arrowLine_${index}`)[0];
                    // if (!arrowLine) {
                    //     return;
                    // }
                    console.log(`playZoominAnimation index:${index}`)
                    let points = arrowLine.getPoints();
                    let startPointer = {
                        x: points[0],
                        y: points[1]
                    }
                    let endPointer = {
                        x: points[2],
                        y: points[3]
                    }
                    let slope = (endPointer.y - startPointer.y) / (endPointer.x - startPointer.x); //斜率

                    endPointer = {
                        x: points[2] * (1 + rate * 3), //需要拉近 坐标变小
                        y: points[3] * (1 + rate * 3)
                    }
                    if (Math.abs(endPointer.x) >= rootWidth / 2 - userImgSize / 2) {
                        if (endPointer.x < 0) {
                            endPointer.x += Math.abs(endPointer.x) - (rootWidth / 2 - userImgSize / 2);
                        } else {
                            endPointer.x -= Math.abs(endPointer.x) - (rootWidth / 2 - userImgSize / 2);
                        }
                        if (endPointer.x == startPointer.x) {
                            endPointer.y -= userImgSize / 2;
                        }
                        else {
                            endPointer.y = slope * endPointer.x;
                        }
                    }
                    if (Math.abs(endPointer.y) >= rootHeight / 2 - userImgSize - 30) {
                        if (endPointer.y < 0) {
                            endPointer.y += Math.abs(endPointer.y) - (rootHeight / 2 - userImgSize - 30);
                        } else {
                            endPointer.y -= Math.abs(endPointer.y) - (rootHeight / 2 - userImgSize - 30);
                        }
                        endPointer.x = endPointer.y / slope;
                    }
                    var tweenArrowLine = new Konva.Tween({
                        node: arrowLine,
                        duration: 0.8,
                        opacity: 1,
                        stroke: '#EE8000',
                        points: [startPointer.x, startPointer.y, endPointer.x, endPointer.y],
                        onFinish: function () {
                            // remove all references from Konva
                            tweenArrowLine.destroy();
                        }
                    });

                    let userCircle = stage.find('#userCircle_' + index)[0];
                    let angle = 360 / MaxCount * index;// 算出每一个对象所要显示在圆周上的角度
                    let circlePointer = getPointByAngle(endPointer, userImgSize / 2, angle);
                    var tweenUserCircle = new Konva.Tween({
                        node: userCircle,
                        duration: 0.8,
                        opacity: 1,
                        x: circle.getX() + circlePointer.x,
                        y: circle.getY() + circlePointer.y,
                        scaleX: userCircle.getAbsoluteScale().x * (1 + rate - 0.2),
                        scaleY: userCircle.getAbsoluteScale().y * (1 + rate - 0.2),
                        fillPatternScale: {
                            x: 1,
                            y: 1,
                        },
                        onFinish: function () {
                            // remove all references from Konva
                            tweenUserCircle.destroy();
                        }
                    });
                    try {
                        let signedUser = stage.find('#signedUser_' + index)[0];
                        var tweenSignedUser = new Konva.Tween({
                            node: signedUser,
                            duration: 0.8,
                            opacity: 1,
                            scaleX: signedUser.getAbsoluteScale().x * (1 + rate),
                            scaleY: signedUser.getAbsoluteScale().y * (1 + rate),
                            x: circle.getX() + circlePointer.x - userCircle.getRadius() / 2 + 80,
                            y: circle.getY() + circlePointer.y - userCircle.getRadius() / 2 + 30,
                            onFinish: function () {
                                // remove all references from Konva
                                tweenSignedUser.destroy();
                            }
                        });
                    } catch (e) {
                        console.log(e);
                    }

                    try {
                        let vipImage = stage.find('#vipImage_' + index)[0];
                        var tweenVipImage = new Konva.Tween({
                            node: vipImage,
                            duration: 0.8,
                            opacity: 1,
                            scaleX: vipImage.getAbsoluteScale().x * (1 + rate),
                            scaleY: vipImage.getAbsoluteScale().y * (1 + rate),
                            x: circle.getX() + circlePointer.x - userCircle.getRadius() / 2 + 80,
                            y: circle.getY() + circlePointer.y - userCircle.getRadius() / 2,
                            onFinish: function () {
                                // remove all references from Konva
                                tweenVipImage.destroy();
                            }
                        });
                    } catch (e) {
                        console.log(e);
                    }

                    try {
                        let group = stage.find('#group_' + index)[0];
                        var tweenGroup = new Konva.Tween({
                            node: group,
                            duration: 0.8,
                            opacity: 1,
                            onFinish: function () {
                                tweenGroup.destroy();
                            }
                        });
                    } catch (e) {
                        console.log(e);
                    }

                    let tid = setTimeout(function () {
                        tweenUserCircle.play();
                        tweenSignedUser.play();
                        tweenVipImage.play();
                        tweenGroup.play();
                        tweenArrowLine.play();
                        window.clearTimeout(tid);
                    }, 0);
                } catch (ex) {
                    console.log(ex);
                    // layer.remove();
                    // layer.destroy();
                    // layer = null;
                    // layer = new Konva.Layer();
                    // stage.add(layer);
                }

            },

            //移除人员
            playAnimationToReset(item) {
                try {
                    let index = item.index;
                    console.log(`playAnimationToReset index: ${index}`);
                    let arrowLine = stage.find('#arrowLine_' + index)[0];
                    if (arrowLine) {
                        arrowLine.remove();
                        arrowLine.destroy();
                        arrowLine = null;
                    }
                    let group = stage.find('#group_' + index)[0];
                    if (group) {
                        group.remove();
                        group.destroy();
                        group = null;
                    }
                    let userCircle = stage.find('#userCircle_' + index)[0];
                    if (userCircle) {
                        userCircle.remove();
                        userCircle.destroy();
                        userCircle = null;
                    }

                    let signedUser = stage.find('#signedUser_' + index)[0];
                    if (signedUser) {
                        signedUser.remove();
                        signedUser.destroy();
                        signedUser = null;
                    }

                    let vipImage = stage.find('#vipImage_' + index)[0];
                    if (vipImage) {
                        vipImage.remove();
                        vipImage.destroy();
                        vipImage = null;
                    }
                    stage.draw();
                } catch (e) {
                    console.log(e);
                }
            },

            playAnimationToSmall(item) {
                try {
                    let index = item.index;
                    console.log(`playAnimationToSmall index: ${index}`);
                    let angle = 360 / MaxCount * index;// 算出每一个对象所要显示在圆周上的角度
                    let startPointer = getPointByAngle({x: 0, y: 1}, circle.getRadius(), angle); //线起始点，随着角度变化
                    var endPointer = generateEndPointer(index, startPointer);
                    let arrowLine = stage.find('#arrowLine_' + index)[0];
                    var tweenArrowLine = new Konva.Tween({
                        node: arrowLine,
                        x: circle.getX(),//圆 心
                        y: circle.getY(),//圆 心
                        duration: 1,
                        opacity: 0.5,
                        zIndex: -1000,
                        stroke: '#DF6911',
                        points: [startPointer.x, startPointer.y, endPointer.x, endPointer.y],
                        onFinish: function () {
                            // remove all references from Konva
                            tweenArrowLine.destroy();
                        }
                    });

                    let group = stage.find('#group_' + index)[0];
                    var tweenGroup = new Konva.Tween({
                        node: group,
                        duration: 1,
                        opacity: 1,
                        onFinish: function () {
                            tweenGroup.destroy();
                        }
                    });
                    let userCircle = stage.find('#userCircle_' + index)[0];
                    let circlePointer = getPointByAngle(endPointer, userImgSize / 2, angle);
                    var tweenUserCircle = new Konva.Tween({
                        node: userCircle,
                        x: circle.getX() + circlePointer.x,
                        y: circle.getY() + circlePointer.y,
                        duration: 1,
                        opacity: 0.5,
                        scaleX: 1,
                        scaleY: 1,
                        onFinish: function () {
                            // remove all references from Konva
                            tweenUserCircle.destroy();
                        }
                    });

                    let signedUser = stage.find('#signedUser_' + index)[0];
                    var tweenSignedUser = new Konva.Tween({
                        node: signedUser,
                        x: circle.getX() + circlePointer.x - userImgSize / 2 / 2 + 70,
                        y: circle.getY() + circlePointer.y - userImgSize / 2 / 2 + 30,
                        duration: 1,
                        opacity: 0.5,
                        scaleX: 1,
                        scaleY: 1,
                        onFinish: function () {
                            // remove all references from Konva
                            tweenSignedUser.destroy();
                        }
                    });
                    let vipImage = stage.find('#vipImage_' + index)[0];
                    var tweenVipImage = new Konva.Tween({
                        node: vipImage,
                        x: circle.getX() + circlePointer.x - userImgSize / 2 / 2 + 70,
                        y: circle.getY() + circlePointer.y - userImgSize / 2 / 2,
                        duration: 1,
                        opacity: 0.5,
                        scaleX: 1,
                        scaleY: 1,
                        onFinish: function () {
                            // remove all references from Konva
                            tweenVipImage.destroy();
                        }
                    });
                    let tid = setTimeout(function () {
                        tweenSignedUser.play();
                        tweenGroup.play();
                        tweenArrowLine.play();
                        tweenVipImage.play();
                        tweenUserCircle.play()
                        window.clearTimeout(tid);
                    }, 300);
                    stage.draw();
                } catch (e) {
                    console.log(e);
                }
            }
        },

        computed: {},
        filters: {},
        created: function () {
            console.log('created')
        },
        mounted: function () {
            console.log('mounted')
            // currentFakeDataInterval = setInterval(() => {
            //     //Test TODO
            //     _this.updateData([
            //         {
            //             "birthday": "string",
            //             "company": "string",
            //             "employed_date": "string",
            //             "id": "string",
            //             "identity_number": "string",
            //             "name": "Hello LHF",
            //             "phone": "string",
            //             "remark": "string",
            //             "visit_end_timestamp": 0,
            //             "visit_purpose": "0",
            //             "visit_start_timestamp": 0,
            //             "visit_time_type": "0",
            //             "visitee_name": "string",
            //             "photo": 'https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1545472443393&di=5d928dd483b57fdd7f36f79042b58bb7&imgtype=0&src=http%3A%2F%2Fb-ssl.duitang.com%2Fuploads%2Fitem%2F201408%2F13%2F20140813233515_Ge43P.jpeg '//require('../assets/img/male.png'),
            //         },
            //         {
            //             "birthday": "string",
            //             "company": "string",
            //             "employed_date": "string",
            //             "id": "string",
            //             "identity_number": "string",
            //             "name": "Hello HT",
            //             "phone": "string",
            //             "remark": "string",
            //             "visit_end_timestamp": 0,
            //             "visit_purpose": "0",
            //             "visit_start_timestamp": 0,
            //             "visit_time_type": "0",
            //             "visitee_name": "string",
            //             "photo": 'https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1545472443393&di=5d928dd483b57fdd7f36f79042b58bb7&imgtype=0&src=http%3A%2F%2Fb-ssl.duitang.com%2Fuploads%2Fitem%2F201408%2F13%2F20140813233515_Ge43P.jpeg'//require('../assets/img/male.png'),
            //         }
            //     ])
            // }, 10 * 1000)

            currentInterval = setInterval(function updateTime() {
                try {
                    let delaytime = 15;
                    if (vipsignList.length >= MaxCount - 2) {
                        delaytime = 5;
                    }
                    else if (vipsignList.length >= MaxCount - 5 && vipsignList.length < MaxCount - 2) {
                        delaytime = 10;
                    }
                    if (_this.isLoading) {
                        return;
                    }
                    for (let i = 0; i < vipsignList.length; i++) {
                        if (vipsignList[i].updateTime) {
                            let dtime = new Date() - vipsignList[i].updateTime;  // 计算时间差
                            let diffTimes = Math.floor(dtime / 1000); //算出总的秒数差值
                            if (diffTimes >= delaytime) {//delaytime 秒内没有人刷卡，则部门变小回退到原来位置
                                if (_this.isLoading) {
                                    break;
                                }
                                _this.playAnimationToSmall(vipsignList[i]);
                            }
                            if (vipsignList.length >= HistoryMAXCOUNT) {
                                if (diffTimes >= delaytime * 2) {//delaytime 秒内没有人刷卡，则部门变小回退到原来位置
                                    if (_this.isLoading) {
                                        break;
                                    }
                                    _this.playAnimationToReset(vipsignList[i]);
                                    vipsignList.splice(i, 1);
                                }
                            }
                        }
                    }
                } catch (ex) {
                    console.log(ex);
                }

            }, 5 * 1000);//定时器每段时间检查一次
        },
        destroyed: function () {
            if (vipsignList && vipsignList.length > 0) {
                sessionStorage.setItem("sessionList", JSON.stringify(vipsignList));
            }
            vipsignList = [];
            window.clearInterval(currentInterval);
            window.clearInterval(currentFakeDataInterval);
            layer.destroy();
            dateLayer.destroy();
            stage.destroy();
        }
    }

</script>
<style>
    @-webkit-keyframes animtran {
        from {
            transform: rotateZ(0deg);
        }
        to {
            transform: rotateZ(360deg);
        }
    }

    span {
        text-align: center;
        font-family: "SquareFont";
        color: white;
    }

    div {
        font-family: "SquareFont";
    }
</style>

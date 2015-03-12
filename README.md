#Description

While i was working on few projects i have come across few situations where i had to write some directives.
Here i want to mention few of the directives here. These might be useful for you.
Here i also wants mention the other directives which i have used in few projects, but not created by me.
I am mentioning here, so that you don't need to find some other place to find these directives. 


- ### standard-time-meridian
#### Description : 
Converts *epoch time* to *HH:MM:AM/PM* 12-hour format

#####HTML
````HTML
<standard-time-meridian etime='slots[0].epochTime'></standard-time-meridian>
````

#####Java Script
````javascript
.directive('standardTimeMeridian', function () {
    return {
        restrict: 'AE',
        replace: true,
        scope: {
            etime: '=etime'
        },
        template: "<strong>{{stime}}</strong>",
        link: function (scope, elem, attrs) {

            scope.stime = epochParser(scope.etime, 'time');

            function prependZero(param) {
                if (String(param).length < 2) {
                    return "0" + String(param);
                }
                return param;
            }

            function epochParser(val, opType) {
                if (val === null) {
                    return "00:00";
                } else {
                    var meridian = ['AM', 'PM'];

                    if (opType === 'time') {
                        var hours = parseInt(val / 3600);
                        var minutes = (val / 60) % 60;
                        var hoursRes = hours > 12 ? (hours - 12) : hours;

                        var currentMeridian = meridian[parseInt(hours / 12)];

                        return (prependZero(hoursRes) + ":" + prependZero(minutes) + " " + currentMeridian);
                    }
                }
            }

            scope.$watch('etime', function (newValue, oldValue) {
                scope.stime = epochParser(scope.etime, 'time');
            });

        }
    };
})
````

### standard-time-no-meridian
#### Description :
Converts *epoch time* to *HH:MM* 24-hour format

##### HTML
````html
<standard-time-no-meridian etime='slots[1].epochTime'></standard-time-no-meridian>
````

##### Java Script
````javascript
.directive('standardTimeNoMeridian', function () {
    return {
        restrict: 'AE',
        replace: true,
        scope: {
            etime: '=etime'
        },
        template: "<strong>{{stime}}</strong>",
        link: function (scope, elem, attrs) {

            scope.stime = epochParser(scope.etime, 'time');

            function prependZero(param) {
                if (String(param).length < 2) {
                    return "0" + String(param);
                }
                return param;
            }

            function epochParser(val, opType) {
                if (val === null) {
                    return "00:00";
                } else {
                    if (opType === 'time') {
                        var hours = parseInt(val / 3600);
                        var minutes = (val / 60) % 60;

                        return (prependZero(hours) + ":" + prependZero(minutes));
                    }
                }
            }

            scope.$watch('etime', function (newValue, oldValue) {
                scope.stime = epochParser(scope.etime, 'time');
            });

        }
    };
})
````

### date-string-to-epoch
#### Description :
Converts *string* format to *epoch time*

##### HTML
````html
<span date-string-to-epoch='{{dateString}}'></span>
````

##### Java Script
````javascript
.directive('dateStringToEpoch', function() {
  return {
    scope: {
      ipDate: '@dateStringToEpoch'
    },
    template: '<strong>{{stringDate}}</strong>',
    link: function(scope, elem, attrs) {
      var inputString = scope.ipDate;

      var arr = inputString.split(/[ ,]/g);
      arr = ["Jan", "1", "", "2015"];
      arr.splice(2, 1);

      var monthArr = ['Jan', 'Feb', 'March', 'April', 'May', 'June', 'July', 'Aug', 'Sept', 'Oct', 'Nov', 'Dec'];
      var index = monthArr.indexOf(arr[0]);
      var epochTime = new Date(arr[2], index, arr[1]);

      scope.stringDate = +epochTime;
    }
  }
});
````

### ionic-time-picker
#### Description :
It was initially implemented as a directive and later converted to a **bower component**.
You can check out the bower component [here](https://github.com/rajeshwarpatlolla/ionic-timepicker)

##### HTML
````html
<ionic-time-picker etime="slots[0].epochTime" format="12" step="15">    
    {{slots[0].epochTime}}
</ionic-time-picker>
````

##### Java Script
````javascript
.directive('ionicTimePicker', function ($ionicPopup) {
    return {
        restrict: 'AE',
        replace: true,
        scope: {
            etime: '=etime',
            format: '=format',
            step: '=step'
        },
        link: function (scope, element, attrs) {

            element.on("click", function () {

                var obj = {epochTime: scope.etime, step: scope.step, format: scope.format};

                scope.time = { hours: 0, minutes: 0, meridian: "" };

                var objDate = new Date(obj.epochTime * 1000);       // Epoch time in milliseconds.

                scope.increaseHours = function () {
                    if (obj.format == 12) {
                        if (scope.time.hours != 12) {
                            scope.time.hours += 1;
                        } else {
                            scope.time.hours = 1;
                        }
                    }
                    if (obj.format == 24) {
                        if (scope.time.hours != 23) {
                            scope.time.hours += 1;
                        } else {
                            scope.time.hours = 0;
                        }
                    }
                };

                scope.decreaseHours = function () {
                    if (obj.format == 12) {
                        if (scope.time.hours > 1) {
                            scope.time.hours -= 1;
                        } else {
                            scope.time.hours = 12;
                        }
                    }
                    if (obj.format == 24) {
                        if (scope.time.hours > 0) {
                            scope.time.hours -= 1;
                        } else {
                            scope.time.hours = 23;
                        }
                    }
                };

                scope.increaseMinutes = function () {
                    if (scope.time.minutes != (60 - obj.step)) {
                        scope.time.minutes += obj.step;
                    } else {
                        scope.time.minutes = 0;
                    }
                };

                scope.decreaseMinutes = function () {
                    if (scope.time.minutes != 0) {
                        scope.time.minutes -= obj.step;
                    } else {
                        scope.time.minutes = 60 - obj.step;
                    }
                };

                if (obj.format == 12) {

                    scope.time.meridian = (objDate.getUTCHours() >= 12) ? "PM" : "AM";
                    scope.time.hours = (objDate.getUTCHours() > 12) ? ((objDate.getUTCHours() - 12)) : (objDate.getUTCHours());
                    scope.time.minutes = (objDate.getUTCMinutes());

                    if (scope.time.hours == 0 && scope.time.meridian == "AM") {
                        scope.time.hours = 12;
                    }

                    scope.changeMeridian = function () {
                        scope.time.meridian = (scope.time.meridian === "AM") ? "PM" : "AM";
                    };

                    $ionicPopup.show({
                        templateUrl: '../templates/time-picker-12-hour.html',
                        title: '<strong>12-Hour Format</strong>',
                        subTitle: '',
                        scope: scope,
                        buttons: [
                            { text: 'Cancel' },
                            {
                                text: 'Set',
                                type: 'button-positive',
                                onTap: function (e) {

                                    scope.loadingContent = true;

                                    var totalSec = 0;

                                    if (scope.time.hours != 12) {
                                        totalSec = (scope.time.hours * 60 * 60) + (scope.time.minutes * 60);
                                    } else {
                                        totalSec = scope.time.minutes * 60;
                                    }

                                    if (scope.time.meridian === "AM") {
                                        totalSec += 0;
                                    } else if (scope.time.meridian === "PM") {
                                        totalSec += 43200;
                                    }
                                    scope.etime = totalSec;
                                }
                            }
                        ]
                    })

                }

                if (obj.format == 24) {

                    scope.time.hours = (objDate.getUTCHours());
                    scope.time.minutes = (objDate.getUTCMinutes());

                    $ionicPopup.show({
                        templateUrl: '../templates/time-picker-24-hour.html',
                        title: '<strong>24-Hour Format</strong>',
                        subTitle: '',
                        scope: scope,
                        buttons: [
                            { text: 'Cancel' },
                            {
                                text: 'Set',
                                type: 'button-positive',
                                onTap: function (e) {

                                    scope.loadingContent = true;

                                    var totalSec = 0;

                                    if (scope.time.hours != 24) {
                                        totalSec = (scope.time.hours * 60 * 60) + (scope.time.minutes * 60);
                                    } else {
                                        totalSec = scope.time.minutes * 60;
                                    }
                                    scope.etime = totalSec;
                                }
                            }
                        ]
                    })

                }

            });

        }
    };
});
````


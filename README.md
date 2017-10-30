# Pagination
依赖于bootstrap.css ,jquery-1.1X.X.js 
特点：封装度高 引用简单 适应性尚可
HTML:
```
 <div class='container'>
   <div id="tab"></div>
   </div>
  ```
JS引用方式：
```
Pagination({
   'selector': '#tab',
   'json': data, //json格式                       
   'callback': function (elem) {                         
                //elem为$('#tab'),可在此处进行表格其他操作

                        }
                    });
   
  ```
方法源码：
```
var Pagination = function (obj) {
     /*
        depend:jquery-1.11.3.js  bootstrap.css

        return $(table)
        updateDate:17-9-26
        Author:JG Yu
        */
    if (this instanceof Pagination) {
        this.config = {

            'selector': '#table1',
            'json': [],
            'rows': 8,
            'class': 'table table-hover  table-bordered',
            'id': '_' + (0 | (10000 * Math.random())),
            'callback': function () { }
        };
        this.init(obj);
    } else {
        return new Pagination(obj)
    }


    return $(this.config.selector)
}
Pagination.prototype = {
    constructor: Pagination,
    init: function () {
        this.config = $.extend(this.config, arguments[0] || {});
        this.readJson();

    }
    ,
    readJson: function () {
        var self = this, obj = self.config,
            a = obj['json'].length, b = obj['rows'];
        a > b ? self.pages(a, b) : self.tables(obj['json'], { class: 'table table-hover  table-bordered' }, 1,obj.callback);
    }
    ,
    pages: function () {
        var self = this, obj = self.config;
        $(obj['selector']).siblings('.pagesBox').remove();
        $(obj['selector']).before(
            '<div class="pagesBox"><ul class="pagination" > <li class="ps_loops" type=\'-1\'><a  href="javascript:;">&laquo;</a></li>' +
            (function (a, b) {
                var c = Math.ceil(a / b);
                var d = '';
                for (var i = 1; i < c + 1; i++) {
                    d += '<li class="ps_body"><a  href="javascript:;">' + i + '</a></li>'
                }
                return d;
            })(arguments[0] || 2, arguments[1] || 1)




            + ' <li class="ps_loops" type=\'1\'><a href="javascript:;">&raquo;</a></li></ul></div>');


        self.bindEvt(obj['selector']);
        $(obj['selector']).siblings('.pagesBox').find('.ps_body').eq(0).trigger('click');
         }
    ,
    bindEvt: function (selector) {
        var self = this, obj = self.config;
        $(selector).parent().delegate('.ps_body', 'click', function () {
            $(this).parents('.pagination').find('.active').removeClass('active');
            $(this).addClass('active');
          self.tables(obj['json'], { class: 'table table-hover  table-bordered' }, +$(this).text(),obj.callback);


        })
        $(selector).parent().delegate('.ps_loops', 'click', function () {
            var that = + $(this).attr('type');
            var len = $(this).parents('.pagination').find('.ps_body').length;
            var nowIndex = (+$(this).siblings('.active').find('a').text()) - 1;
            var nextVal = nowIndex + that;
            nextVal === len ? nextVal = 0 : nextVal;
            nextVal < 0 ? nextVal = len - 1 : nextVal;

            $(this).parents('.pagination').find('.ps_body').eq(nextVal).trigger('click');

        })

    }
    ,
    tables: function () {
        /**
         0 ,json
         1 ,class and id config
         2 ,current index
        3 ,callback
         */

        var objs = arguments[0];
        var length = objs.length,
            that = this;
        var multi = arguments[2] || 1; multi -= 1;
        var thisRows = that.config.rows,tabs="" ;
        if (typeof objs === "object") {


            //*****************************************
             tabs = "<table id='" + that.config.id + "' class='" + that.config.class + "'>";

                tabs += "<tr>";
                for (var items in objs[0]) {

                    tabs += "<th>" + items + "</th>";
                }
                tabs += "</tr>";

            var len = (multi + 1) * thisRows > objs.length ? objs.length : (multi + 1) * thisRows, exp = [], tdHtml = "", ml = this.maxLen;
            for (var i = multi * thisRows; i < len; i++) {
                tabs += "<tr >";
                for (var lists in objs[i]) {
                    exp = [''];
                    exp.push(objs[i][lists].replace(/\[(.*)\]/, function (a, b, c, d) { exp.push(b); return ''; }));
                    tdHtml = exp.slice(-1)[0].retDOM(exp.slice(-2)[0]);
                    (!/\<.*\>/.test(tdHtml) && tdHtml.length > ml) ?
                        (tabs += "<td  class='" + exp.slice(-2)[0] + "' data-title='Info:" + tdHtml + "'>" +
                            tdHtml.substr(0, ml) + "...</td>")
                        : (tabs += "<td class='" + exp.slice(-2)[0] + "'>" + tdHtml + "</td>");//54-62-08303-016G
                }
                tabs += "</tr>";
            }
            tabs += "</table>";

        } else {

            tabs= 'format error';
        }
        $(that.config.selector).html(tabs);
        arguments[3]($(that.config.selector).find('table'))||"";

    }

}
```

图片浏览前预加载图片，使用jQuery封装插件，其中有三个实例展示。
图片无序预加载，翻页展示，loading显示百分比进度。
qq表情无序预加载，打开展示，显示loading。
漫画有序预加载，翻页展示。
初始化代码

function PreLoad(imgs,options){
    this.imgs = ((typeof imgs === 'string')?[imgs]:imgs);
    this.opts = $.extend({},PreLoad.DEFAULTS,options);
    if(this.opts.order === 'ordered'){
        //有序预加载
        this._ordered();
    }else{
        //无序预加载
        this._unoredered();//加下划线的意思是表明这个方法只在内部使用
    }

}
PreLoad.DEFAULTS = {
    order:'unordered',//无序预加载
    each:null,//每张图片加载完毕后执行的方法
    all:null,//所有图片加载完毕后执行的方法
};
无序预加载代码

PreLoad.prototype._unoredered = function(){
    //无序加载
    var imgs = this.imgs,
        opts = this.opts,
        count = 0,
        len = imgs.length;

    $.each(imgs , function(i,src) {
        if(typeof src != 'string') return;

        var imgObj = new Image();
        $(imgObj).on('load error', function() {
            //$progress.html(Math.round((count+1)/len*100)+'%');具体操作应该单拿出来
            //
            opts.each && opts.each(count);//如果直接写opts.each()(加了括号)就是立刻执行操作，但是如果这个方法不存在则会报错，所以应该先判断each方法是否存在

            if(count >= len-1 ){
                // console.log(count);
                // setTimeout(function(){
                //  $(".loading").hide();
                // },2000);
                // document.title = '1/' + len;具体操作需要单独拿出来
                // 
                opts.all && opts.all();
            }
            count++;
        });
        imgObj.src = src;
    });
}
有序预加载代码

PreLoad.prototype._ordered = function(){
    //有序加载
    var imgs = this.imgs,
        opts = this.opts,
        count = 0,//当前加载到的图片
        len = imgs.length;

        load();
    //有序预加载
    function load(){
        var imgObj =  new Image();
        $(imgObj).on('load error' , function(){
            opts.each && opts.each();
            if (count >=len) {
                //所有图片加载完成
                opts.all && opts.all();
            }else{
                load();
            };
            count++;
        })

        imgObj.src = imgs[count];
    }
}
扩展方法

$.extend({
    preload :function(imgs,opts){
        new PreLoad(imgs , opts);
    }
})
调用方法

 $.preload(imgs,{
  order: '',
  each: function(count) {
        //每张图片加载完成后执行函数
  },
  all: function() {
        //所有图片加载完成后执行的函数
  }
})
});
github地址：

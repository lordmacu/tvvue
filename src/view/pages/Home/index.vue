<i18n src="./i18n/index.json"></i18n>

<template>



    <div class="container-fluid" >

        <div class="row">
            <div class="col-3" v-if="!fullscreenI">
                <div class="preview-movie row" v-if="preview">

                    <div  class="controls col-lg-12 d-none d-sm-block">
                        <table>
                            <tr>
                                <td></td>
                                <td><button v-on:click="jumpUp()"  >Arriba</button></td>
                                <td></td>
                            </tr>
                            <tr>
                                <td><button v-on:click="jumpStep(2)" >Izquierda</button></td>
                                <td class="container-play"><button class="play-button" v-on:click="loadVideo()" >Play</button></td>
                                <td><button v-on:click="jumpStep(1)" > Derecha</button></td>
                            </tr>
                            <tr>
                                <td> <button v-on:click="back()"><- Atras</button></td>
                                <td> <button v-on:click="jumpDown()">Abajo</button></td>
                                <td></td>
                            </tr>
                        </table>


                    </div>


                    <div class="col-lg-12 description-container-movie">
                        <h1>{{preview.title}}</h1>
                        <span>Año - {{preview.year}}</span> - 
                        <span>Duración - {{preview.infoTime}}</span> - 
                        <span>Calidad - {{preview.infoQlty}}</span> 
                        <p v-html="preview.description"></p>
                   <br/>
                        <div class="row">
                             <div class="col-12 container-button-video" v-for="source in sourcesVideo">

                            <button class="btn btn-default  btn-light" v-on:click="showVideoIdentifier(source.identifier)">
                                {{source.nameResource}}
                            </button>
                        </div>
                        </div>
                        
                    </div>


                </div>
            </div>

            <div class="col-lg-9">
                <div  id="movies-container"  nv-scope="menu" class="row movies-container" nv-scope-current v-if='movies.length>0'>
                    
                    <div class="col-12 container-categories">
                        <div class="row">
                            <div class="col-12 col-md-7 col-lg-7">
                                <button class="btn btn-default btn-light" v-on:click="latest()" >Últimas</button>
                        <button class="btn btn-default btn-light" v-on:click="newMovies()">Estrenos</button>
                        <button class="btn btn-default btn-light" v-on:click="ranking()">Ranking</button>
                        <button class="btn btn-default btn-light" v-on:click="popular()">Mas vistas</button>

                            </div>
                            
                            <div class="col-12 col-md-5 col-lg-5">
                                                                                            <br/>

                                <div class="input-group mb-3">
                                    <input type="text" v-model="searchText" class="form-control" placeholder="Buscar por nombre">

                                    <div class="input-group-prepend">
                                      <button class="btn btn-outline-secondary" type="button" id="button-addon1" v-on:click="searchMovie">Buscar</button>
                                    </div>
                                </div>
                            </div>
                        </div>
                        
                        
                  
                    </div>
                    <div nv-el  nv-el-current :class="checkZoom(movies[0])" v-popover:tooltip="'This is a string value'"  v-on:click="overClick(movies[0],0)"  :name="'element_'+0" :id="'element_'+0" :datai="0"  :style="'background-image:url('+movies[0].image+')'" >

                         <div class="content-movie row"  >
                            {{movies[0].infoQlty}}
                        </div>

                    </div>


                    <div v-for="(movie,index) in movies"  :class="checkZoom(movie)" :name="'element_'+index"  v-on:click="overClick(movie,index)"  v-if="index!=0"  :id="'element_'+index"    :datai="index"   nv-el  :style="'background-image:url('+movie.image+')'" >
                        <div class="content-movie row ss">
                            {{movie.infoQlty}}
                        </div>
                    </div>
                    
                    <div class="col-12 load-more-container">
                        <button class="btn btn-default btn-light" v-on:click="loadMore">Cargar mas</button>
                    </div>



                </div>

                <div class="container-video " v-if="frameVideo">
                    <div class="row">
                       
                    <div class="col-12 container-video-frame" >
                        <iframe id="myvideo" :class="fullscreenIframe()" width="100%" height="500" :data-src="frameVideo" :src="frameVideo" frameborder="0" allowfullscreen="true"></iframe>
                   <hr/>
                   <button v-if="!fullscreenI" v-on:click="fullscreen" class="btn btn-default btn-light">FullScreen</button>            

                    </div>
                    </div>
                    
                   
                </div>
            </div>
        </div>




        <div class="spaceload"></div>
    </div>

</template>

<script>


    export default {
        name: 'Home',
        data() {
            return {
                movies: [],
                preview: null,
                jumpValue: 0,
                jumpStepValue: 0,
                sourcesVideo: [],
                players:[],
                frameVideo:null,
                paginator:1,
                category:0,
                searchText:null,
                fullscreenI:false
            }
        },
        created() {
            
         document.onkeydown = function(evt) {
    evt = evt || window.event;
    var isEscape = false;
    if ("key" in evt) {
        isEscape = (evt.key == "Escape" || evt.key == "Esc");
    } else {
        isEscape = (evt.keyCode == 27);
    }
    if (isEscape) {
        alert("Escape");
    }
};   
            /*
             setInterval(() => {
             
             var current= $(".nv-scope-current").find(".nv-el-current").attr("datai");                
             for(var l = 0; l < this.movies.length; l++){
             this.movies[l].current=false;
             }
             
             if(this.movies[current]){
             this.movies[current].current=true;
             
             this.$scrollTo("#element_"+current);
             
             
             }
             
             
             
             }, 500);*/



            document.body.addEventListener('nv-left', (event) => {


                this.tabMovie(event, false);

            });

            document.body.addEventListener('nv-up', (event) => {
                this.tabMovie(event, true);

            });

            document.body.addEventListener('nv-down', (event) => {
                this.tabMovie(event, true);

            });



            document.body.addEventListener('nv-right', (event) => {
                this.tabMovie(event, false);

            });






                this.paginator=1;

            this.loadHome();

            console.log("aqui mano", $);
        },
        computed: {

        },
        methods: {
            fullscreenIframe(){
                if(this.fullscreenI){
                    return "fullscreen";
                }
            },
            fullscreen(){
                this.fullscreenI=true;
              
              
              try {

      var elem = document.documentElement;

if (elem.requestFullscreen) {
    elem.requestFullscreen();
  } else if (elem.mozRequestFullScreen) { /* Firefox */
    elem.mozRequestFullScreen();
  } else if (elem.webkitRequestFullscreen) { /* Chrome, Safari and Opera */
    elem.webkitRequestFullscreen();
  } else if (elem.msRequestFullscreen) { /* IE/Edge */
    elem.msRequestFullscreen();
  }
     


}
catch(err) {
alert( err.message);
        }
            
        
            },
            searchMovie(){
                
                this.paginator=1;
                    this.movies=[];
                
                if(!this.searchText){
                    this.category=0;

                    this.loadHome();
                    return false;
                }
                 this.movies=[];
                 this.axios.get("https://www.alquilerdirecto.com.ar/searchMovie?search="+this.searchText, ).then((response) => {

                var responseHtml = $(response.data);
                var listMovies = responseHtml.find(".MovieList li");

                for (var i = 0; i < listMovies.length; i++) {
                    var item = $(listMovies[i]);
                    var link = item.find("a").attr("href");
                    var title = item.find(".Title").html();
                    var year = item.find(".Year").html();
                    var image = item.find("figure img").attr("src").replace("-160x242", "");
                    var image = image.replace("-55x85", "");
                    var infoTime = item.find(".Info .Time").html();
                    var infoQlty = item.find(".Info .Qlty").html();
                    var description = item.find(".Description").html();

                    this.movies.push({
                        link: link,
                        title: title,
                        year: year,
                        image: image,
                        infoTime: infoTime,
                        infoQlty: infoQlty,
                        description: description,
                        current: false,
                        index: i
                    });

                }

                this.preview = this.movies[0];

                setTimeout(function () {
                    navigation.refresh();

                }, 1000);

                });
            },
            
            latest(){
                    this.paginator=1;
                    this.movies=[];
                this.category=0;
                this.loadHome();
            },
            newMovies(){
                                    this.movies=[];

                this.paginator=1;
                this.category="estrenos";
                this.loadHome();
            },
            ranking(){
                                    this.movies=[];

                    this.paginator=1;
                this.category="peliculas-mas-valoradas";
                this.loadHome();
            },
            popular(){
                                    this.movies=[];

                    this.paginator=1;
                this.category="peliculas-mas-vistas";
                this.loadHome();
            },
            
            
            
            loadHome(){
              
            this.axios.get("https://alquilerdirecto.com.ar/getHomeMovies?paginator="+this.paginator+"&category="+this.category, ).then((response) => {

                var responseHtml = $(response.data);
                var listMovies = responseHtml.find(".MovieList li");

                for (var i = 0; i < listMovies.length; i++) {
                    var item = $(listMovies[i]);
                    var link = item.find("a").attr("href");
                    var title = item.find(".Title").html();
                    var year = item.find(".Year").html();
                    var image = item.find("figure img").attr("src").replace("-160x242", "");
                    var image = image.replace("-55x85", "");
                    var infoTime = item.find(".Info .Time").html();
                    var infoQlty = item.find(".Info .Qlty").html();
                    var description = item.find(".Description").html();

                    this.movies.push({
                        link: link,
                        title: title,
                        year: year,
                        image: image,
                        infoTime: infoTime,
                        infoQlty: infoQlty,
                        description: description,
                        current: false,
                        index: i
                    });

                }

                this.preview = this.movies[0];

                setTimeout(function () {
                    navigation.refresh();

                }, 1000);


                console.log(this.movie);

            })  
            },
            back(){
                this.frameVideo=null;
            },
            loadMore(){
                this.paginator++;
                this.loadHome();
            },

            loadVideo() {
                this.loadUrl(this.preview.link);
            },
            
            getVideoFrame(url){
                this.frameVideo=url;
                
                return false;
                var url=url.split("file=");
                if(url.length==2){
                this.frameVideo=url[1];
                }else{
                this.frameVideo=url[0];
                }
                /*this.axios.get("https://alquilerdirecto.com.ar/getUrl?url=" + encodeURI(url), ).then((response) => {

                });*/
            },
            loadUrl(url) {



                this.axios.get("https://alquilerdirecto.com.ar/getUrl?url=" + encodeURI(url), ).then((response) => {
                    var responseHtml = $(response.data);
                    
                    var players=responseHtml.find(".TPlayerTb");
                    
                    for (var ifr = 0; ifr < players.length; ifr++) {

                        var iframe = $(players[ifr]);
                        
                        this.players.push({id:iframe.attr("id"),frame:iframe.find("iframe").attr("data-src")})
                        }
                        console.log(this.players);


                    
                    var videosResources = responseHtml.find(".open_submenu ul li");
                    //    var resource=$(videosResources[i]);
                    this.sourcesVideo=[];
                    for (var i = 0; i < videosResources.length; i++) {

                        var resource = $(videosResources[i]);
                        var identifier = resource.attr("data-tplayernv");
                        var nameResource = resource.find("span span").html();
                        var image = resource.find("img").attr("src");

                        this.sourcesVideo.push({identifier: identifier, nameResource: nameResource, image: image});


                    }
                    
                    
                    this.showVideoIdentifier(this.sourcesVideo[0].identifier);



                })
            },
            showVideoIdentifier(source){
              
                    for (var i = 0; i < this.players.length; i++) {
                        if(this.players[i].id==source){
                               // this.frameVideo=this.players[i].frame;
                                
                                this.getVideoFrame(this.players[i].frame);
                        }
                }
              
            },
            jumpStep(step) {
                if (step == 1) {

                    this.jumpValue = this.preview.index + 1;
                } else {
                    if (this.jumpValue > 0) {
                        this.jumpValue = this.preview.index - 1;
                    }
                }



                this.jump("element_" + this.jumpValue);
                this.preview = this.movies[this.jumpValue];

            },
            checkZoom(movie) {
                if (movie.index == this.preview.index) {
                    return "col-md-3 col-lg-3 col-12 movie-container  zoom";
                } else {
                    return "col-md-3 col-lg-3 col-12 movie-container";
                }
            },
            overmouse(movie, index) {
                /* for(var l = 0; l < this.movies.length; l++){
                 this.movies[l].current=false;
                 }
                 movie.current=true;*/
                this.preview = movie;

            },
            overClick(movie, index) {

                /* for (var l = 0; l < this.movies.length; l++) {
                 this.movies[l].current = false;
                 }
                 
                 movie.current = true;*/
                this.preview = movie;
                
                this.loadVideo();
            },
            jumpUp() {
                if (this.jumpValue > 0) {
                    this.jumpValue = this.jumpValue - 6;
                    this.jump("element_" + this.jumpValue);
                    this.preview = this.movies[this.jumpValue];
                }


            },
            jumpDown() {
                this.jumpValue = this.jumpValue + 6;

                this.jump("element_" + this.jumpValue);
                this.preview = this.movies[this.jumpValue];

            },
            jump(h) {
                var top = document.getElementById(h).offsetTop; //Getting Y of target element
                var top = top - 60;
                console.log(top);
                window.scrollTo(0, top);
                //  document.getElementById(h).focus();

            },
            tabMovie(event, anchor) {


                var element = event.target.id.split("_");

                this.movies[element[1]].current = false;

                var current = $(".nv-scope-current").find(".nv-el-current").attr("datai");

                if (this.movies[current]) {
                    this.movies[current].current = true;
                    this.preview = this.movies[current];
                    if (anchor) {
                        this.jump("element_" + current);
                    }

                }




                /*   
                 for(var l = 0; l < this.movies.length; l++){
                 this.movies[l].current=false;
                 }
                 
                 
                 var element=event.target.id.split("_");
                 
                 this.movies[element[1]].current=true;
                 this.$scrollTo("#element_"+element[1],this,300,this.options);
                 console.log("#element_"+element[1]);*/

            }
        }
    }




</script>

<style lang="scss">
    .Home {
        padding: 50px;
        color: $color-primary;
    }
</style>

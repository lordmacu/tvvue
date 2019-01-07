<i18n src="./i18n/index.json"></i18n>

<template>



    <div class="container-fluid" >


        <div class="preview-movie" v-if="preview">
            <h1>{{preview.title}}</h1>
            <span>Año - {{preview.year}}</span> - 
            <span>Duración - {{preview.infoTime}}</span> - 
            <span>Calidad - {{preview.infoQlty}}</span> 
            <p v-html="preview.description"></p>
        </div>

        <div  id="movies-container"  nv-scope="menu" class="row movies-container" nv-scope-current v-if='movies.length>0'>
            <div nv-el  nv-el-current :class="checkZoom(movies[0])" v-popover:tooltip="'This is a string value'" v-on:mouseover="overmouse(movies[0],0)" v-on:click="overClick(movies[0],0)"  :name="'element_'+0" :id="'element_'+0" :datai="0"  :style="'background-image:url('+movies[0].image+')'" >

                <div class="content-movie row"  >
                    ss  {{movies[0].current}}
                </div>

            </div>


            <div v-for="(movie,index) in movies"  :class="checkZoom(movie)" :name="'element_'+index" v-popover:tooltip="'This is a string value'" v-on:mouseover="overmouse(movie,index)"  v-on:click="overClick(movie,index)"  v-if="index!=0"  :id="'element_'+index"    :datai="index"   nv-el  class="col-2 movie-container" :style="'background-image:url('+movie.image+')'" >
                <div class="content-movie row ss">
                    ss {{movie.current}}
                </div>

               



            </div>

        </div>

        <popover name="moviepop">
            Hello 🎉
        </popover>
    </div>

</template>

<script>


    export default {
        name: 'Home',
        data() {
            return {
                movies: [],
                preview:null
            }
        },
        created() {
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








            this.axios.get("https://alquilerdirecto.com.ar/getHomeMovies", ).then((response) => {

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
                        current: false
                    });

                }
                
                this.preview=this.movies[0];

                setTimeout(function () {
                    navigation.refresh();

                }, 1000);


                console.log(this.movie);

            })

            console.log("aqui mano", $);
        },
        computed: {

        },
        methods: {
            checkZoom(movie){
                if(movie.current){
                    return "col-2 movie-container  zoom";
                }else{
                    return "col-2 movie-container";
                }
            },
            overmouse(movie, index) {
                 for(var l = 0; l < this.movies.length; l++){
                 this.movies[l].current=false;
                 }
                 movie.current=true;

            },
            overClick(movie, index) {

                for (var l = 0; l < this.movies.length; l++) {
                    this.movies[l].current = false;
                }

                movie.current = true;
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
                    this.preview=this.movies[current];
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

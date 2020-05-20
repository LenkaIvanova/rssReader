<template>
  <div class="row justify-center items-center">
    
    <div class="col-12 input_place">
      <q-input 
        v-model="url" 
        label="Zadajte URL..."
        @keyup.enter="fetchAndReload()"/>
    </div>

    <div class="col-12 row justify-center items-center fetch_btn_row">
      <q-btn 
        v-on:click="fetchAndReload()"       
        class="col-12 fetch_btn"
        label="Fetch / Reload" />
    </div>

    <div class="col-12 row justify-center"> 
      
      <div class="col-6 col-md-6 col-xs-12 row justify-center items-center left_side"> 

        <div class="col-12 row justify-center items-center filter">
          <div class="col-12 bolder_font" 
            style="margin-bottom: 10px;">
            Order by
          </div>
          
          <q-btn-dropdown 
            label="Choose"
            class="col-12 choose_btn">
            <q-list>
              <q-item clickable v-close-popup @click="sortDescending()">
                <q-item-section>
                  <q-item-label>PubDate - Descending</q-item-label>
                </q-item-section>
              </q-item>

              <q-item clickable v-close-popup @click="sortAscending()">
                <q-item-section>
                  <q-item-label>PubDate - Ascending</q-item-label>
                </q-item-section>
              </q-item>

            </q-list>
          </q-btn-dropdown>

          
        </div>

        <div class="col-12 row justify-center items-center nazvy">
          <div class="col-12 bolder_font art_part">Articles</div>
          <button 
            v-for="art in rssArticles" 
            :key="art.title"
            v-on:click="article=art" 
            class="text-black col-12 part_titles">
            {{art.pubDate}}, {{art.title}}
        </button>
        </div>
      </div>

      <div class="col-6 col-md-6 col-xs-12 displayed_articles"
        v-if="article">

        <h4>{{article.title}}</h4>
        <q-img
          :src="article.enclosure.url" 
          spinner-color="white"             
          class="responsive"
          style="margin-bottom: 20px;"
        />

        <p class="art_info">Description: {{article.content}}</p>
        <p class="art_info">Publication Date: {{article.pubDate}}</p>
        <p class="art_info"><a :href="article.link" target="_blank">Link</a></p>
      </div>                

    </div>
    
  </div>
</template>

<script>
var Feed = require('rss-to-json');

export default {
  name: 'PageIndex',
  data () {
      return {
          url : '',
          article: null,
          rssArticles: []
      }
  },

  created () { 
    let Parser = require('rss-parser');
    let parser = new Parser();
    let feed;

    (async () => {
        feed = await parser.parseURL(this.url);
        this.rssArticles=feed.items
        console.log(feed.items[0])
    })();
  },

  methods : {  
      getArticleByTitle (title) {
        var nasElement;
        this.rssArticles.forEach(element => {
          if (element.title==title)
            nasElement = element;
        })
        return nasElement;
      },

      fetchAndReload () {
        let Parser = require('rss-parser');
        let parser = new Parser();
        let feed;
        (async () => {
            feed = await parser.parseURL(this.url);
            this.rssArticles=feed.items
            this.article=null
        })();
      },

      sortDescending () {
          this.rssArticles.sort(function(a,b){
            return new Date(b.pubDate) - new Date(a.pubDate);
          });
      },

      sortAscending () {
          this.rssArticles.sort(function(a,b){
            return new Date(b.pubDate) - new Date(a.pubDate);
          }).reverse();
      }
  }
}


</script>
<style scoped> 

  .input_place {
    padding-left: 20px; 
    margin: 10px 25px 5px 25px;
    border: 1px solid #ededed;
    border-radius: 5px;
  }

  .left_side {
    text-align: center;
  }

  .part_titles {
    padding: 10px 30px;
    background: none!important;
    border: none;
    color: #069;
    text-decoration: underline;
    cursor: pointer;
    margin-left: 5px;
  }

  .filter {
    padding: 10px 15px;
    margin: 20px;
  }

  .art_part {
    border-top: 1px solid #ededed;
    border-width: 80%;
    padding-top: 10px ;
    margin: 20px;
  }

  .displayed_articles {
    padding: 10px 20px;
  }

  .bolder_font {
    font-weight: bolder;
    font-size: 1.2rem;
  }

  .choose_btn, .fetch_btn {
    background-color: #e9e9e9;
    border-color: #ebbdbd;
  }

  .art_info {
    font-size: 1.1rem;
  }

@media (min-width: 0) {
  .input_place, .fetch_btn{
    width: 98%;
  }

}
</style>

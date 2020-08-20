<script>
  import { onMount } from "svelte"
  import Router from 'svelte-spa-router'
  import {link, wrap, location, querystring} from 'svelte-spa-router'
  import active from 'svelte-spa-router/active'
  // console.log(active)

  import { bkt, menuHeight, album, pic } from './stores'
  import Home from './routes/Home.svelte'
  import Album from './routes/Album.svelte'
  import NotFound from './routes/NotFound.svelte'

  $: album.set( $location.split('/').slice(2).join('/') ) // Ha Ha
  $: activate( $album || $querystring )

  onMount(function() {
    let menuBar = document.querySelector( '.menu' )

    console.log(`Mbar mounted`)

    // console.dir( menuBar.clientHeight )
    menuHeight.set( menuBar.clientHeight )
  })

  const activate = () => {
    let mItems = document.querySelector("ul.menu")
    if ( mItems ) {
      let anchors = mItems.querySelectorAll("a.anchor")
      anchors.forEach(( el, i ) => {
        if ( el.outerText === $album )
          anchors[i].classList.add('active')
      })
    }
  }
/*
  function handleMousemove(evt) {
    console.log(`MBar mouse enter`)
    //console.log(evt)
  }
*/
  let gallery = bkt+'/album.txt'
  let Albums = []

  onMount(async function() {

    const response = await fetch( gallery )
    const json = await response.json()
    Albums = json
  })

  const routes = {

      '/': Home,

      '/album/:name': Album,

      '*': NotFound
    }

</script>


<style>

	ul.menu {
    position: absolute;
  /*   position: relative; */
	/* 	position: fixed; */

		top: 0;
		left: 0;
		width: 100%;
		list-style-type: none;
		margin: 0;
		padding: 0;
		overflow: hidden;

    background-color:lightcyan;
    background-color: rgba( 230, 230, 230, 0.8 );
	}

	ul.menu li {
		float: left;
	}

	ul.menu li a {
		display: block;
		opacity: .85;
		text-align: center;
		padding: 6px 12px;
		text-decoration: none;
	}

	ul.menu li a:hover {
		color: white;
		background-color: rgba( 130, 130, 130, 0.8 );
	}

	ul.menu li a:active {
		color: white;
		background-color: rgba( 64, 64, 64, 0.8 );
	}

	:global( ul.menu li a.active ) {
		color: white;
		background-color: rgba( 64, 64, 64, 0.8 );
	}
	:global( ul.menu li a ) {
		color: black;
  }

</style>

<!-- on:mousemove={handleMousemove} -->

<Router {routes}  />

<div class = 'menu-bar'>
  <slot></slot>

  <ul class="menu">
    <div>
      <li>
        <a href='/'
          use:link use:active>
          Home
        </a>
      </li>

        {#each Albums as pix}
          <li>
            <a href='/album/{pix.album}'
              use:link use:active
              class='anchor'
            > {pix.album} </a>
          </li>
        {/each}

    </div>
  </ul>

</div>

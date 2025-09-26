<script lang="ts">
  import { onMount } from 'svelte'

  let textsContainer: HTMLElement
  let textSelectorContainer: HTMLElement
  let invisibleTextAnchor: HTMLAnchorElement
  let galleryContainer: HTMLElement

  async function toggleTransition(event: MouseEvent) {
    const btn = (event.target as HTMLElement).closest('button') as HTMLButtonElement | null
    if (!btn) return

    for (const button of textSelectorContainer.querySelectorAll('button')) {
      button.classList.toggle('active', button.id === btn.id)
    }
    for (const container of textsContainer.querySelectorAll<HTMLElement>('.text-container')) {
      container.classList.toggle('active', container.getAttribute('name') === btn.id)
    }

    const isDesktop = window.matchMedia('(min-width: 640px)').matches
    const offset = isDesktop ? 40 : textSelectorContainer.offsetHeight

    const rect = invisibleTextAnchor.getBoundingClientRect()
    const targetY = window.scrollY + rect.top - offset

    const scroller = document.scrollingElement || document.documentElement
    scroller.scrollTo({ top: targetY, behavior: 'smooth' })
  }

  function rotateAndOrderImage() {
    let zIndex = 100

    galleryContainer.querySelectorAll('img').forEach((img) => {
      img.style.zIndex = `${zIndex--}`
      img.style.rotate = `${(Math.random() - 0.5) * 10}deg`
    })
  }

  function reorderImage(event: Event) {
    const target = (event.target as HTMLElement).closest('img') as HTMLImageElement | null
    if (!target || !galleryContainer) return

    const imgs = [...galleryContainer.querySelectorAll<HTMLImageElement>('img')]

    // sort so: target first, then others by current z (desc). If z missing, treat as 0.
    imgs.sort((a, b) => {
      if (a === target) return -1
      if (b === target) return 1
      return (Number(b.style.zIndex) || 0) - (Number(a.style.zIndex) || 0)
    })

    // reassign compact z-indexes: 100, 99, 98, ...
    let z = 100

    for (const img of imgs) {
      img.style.zIndex = String(z--)
    }
  }

  function showNextImageInQueue() {
    if (!galleryContainer) return

    const imgs = [...galleryContainer.querySelectorAll<HTMLImageElement>('img')]
    if (imgs.length < 2) return

    // sort by current z (desc); missing z treated as 0
    imgs.sort((a, b) => (Number(b.style.zIndex) || 0) - (Number(a.style.zIndex) || 0))

    // rotate left: [top, a, b, ...] => [a, b, ..., top]
    const top = imgs.shift()!
    imgs.push(top)

    // reassign compact z: 100, 99, 98, ...
    let z = 100

    for (const img of imgs) {
      img.style.zIndex = String(z--)
    }
  }

  function resizeGalleryContainer() {
    if (galleryContainer) {
      let maxHeight = 0

      galleryContainer.querySelectorAll('img').forEach((img) => {
        let imgHeight = img.height

        if (imgHeight > maxHeight) {
          maxHeight = imgHeight
        }

        galleryContainer.style.height = `${maxHeight + 80}px`
      })
    }
  }

  onMount(() => {
    resizeGalleryContainer()
    rotateAndOrderImage()
    window.addEventListener('resize', resizeGalleryContainer)
  })
</script>

<svelte:head>
  <title>Nestor Schoonbroodt - site commémoratif</title>
  <meta
    name="description"
    content="Site commémoratif en la mémoire de Nestor Schoonbroodt, décédé le 13 septembre 2025 à Desnié."
  />
</svelte:head>

<nav>
  <ul>
    <li><a href="#textes">Textes</a></li>
    <li><a href="#photos">Photos</a></li>
    <li><a href="#remerciements">Remerciements</a></li>
  </ul>
</nav>

<header>
  <!-- prettier-ignore -->
  <video src="/videos/cloud-loop.mp4" autoplay loop muted playsinline disablePictureInPicture></video>
  <blockquote>
    <p>J'aime les nuages... les nuages qui passent... là-bas... les merveilleux nuages !</p>
    <small><author>Charles Baudelaire</author> - <cite>L'Etranger</cite></small>
  </blockquote>
  <enhanced:img
    src="/static/images/nestor-portrait.jpg"
    alt="Portrait de Nestor Schoonbroodt"
    width="200"
  />
  <h1>Nestor Schoonbroodt</h1>
  <p>
    Né à Battice le <date>15/06/1933</date> et décédé à Desnié le <date>13/09/2025</date>
  </p>
  <small>Fondateur de Schoonbroodt Hydraulics SA</small>
</header>
<main>
  <!-- textes -->
  <texts-container bind:this={textsContainer} id="textes">
    <wrapper-container>
      <h2>Textes</h2>
      <p class="introduction">
        Proches et amis ont souhaité partager quelques textes et paroles qui reflètent ta
        sensibilité, ta sagesse et l'amour que nous avons pour toi. Ces textes ont résonné comme
        autant d'échos à ta vie et à l'héritage que tu laisses dans nos cœurs. Ils demeurent ici
        comme une trace de ce moment de recueillement et de souvenir.
      </p>
    </wrapper-container>
    <invisible-text-anchor bind:this={invisibleTextAnchor}></invisible-text-anchor>
    <text-selector-container>
      <!-- svelte-ignore a11y_click_events_have_key_events -->
      <!-- svelte-ignore a11y_no_noninteractive_element_interactions -->
      <ul bind:this={textSelectorContainer} onclick={(event) => toggleTransition(event)}>
        <li>
          <button class="active" id="il-meurt-lentement"> Il meurt lentement </button>
        </li>
        <li>
          <button id="eloge-funebre"> Eloge Funèbre </button>
        </li>
        <!-- 
        <li>
          <button id="quelques-mots-de-cathy">Quelques mots de Cathy</button>
        </li>
        <li>
          <button id="quelques-mots-de-philippe">Quelques mots de Philippe</button>
        </li>
        -->
        <li>
          <button id="quelques-mots-de-andre">Quelques mots de André</button>
        </li>
      </ul>
    </text-selector-container>
    <wrapper-container class="text-container active" name="il-meurt-lentement">
      <h3>Il Meurt Lentement</h3>
      <figure>
        <blockquote>
          <p>
            Il meurt lentement<br />
            Celui qui ne voyage pas<br />
            Celui qui ne lit pas<br />
            Celui qui n'écoute pas de musique<br />
            Celui qui ne sait trouver<br />
            Grâce à ses yeux
          </p>
          <p>
            Il meurt lentement<br />
            Celui qui détruit son amour-propre<br />
            Celui qui ne sait jamais aider
          </p>
          <p>
            Il meurt lentement<br />
            Celui qui devient esclave de l'habitude<br />
            Refaisant tous les jours les mêmes chemins<br />
            Celui qui ne change jamais de repères<br />
            Ne se risque jamais à changer<br />
            La couleur de ses vêtements<br />
            Ou qui ne parle jamais à un inconnu
          </p>
          <p>
            Il meurt lentement<br />
            Celui qui évite la passion<br />
            Et son tourbillon d'émotions<br />
            Celles qui redonnent la lumière dans les yeux<br />
            Et réparent les cœurs blessés
          </p>
          <p>
            Il meurt lentement<br />
            Celui qui ne change pas de cap<br />
            Lorsqu'il est malheureux<br />
            Au travail ou en amour<br />
            Celui qui pas une seule fois dans sa vie<br />
            N'a fui les conseils sensés
          </p>
          <p>
            Vis maintenant<br />
            Risque-toi aujourd'hui<br />
            Agis tout de suite<br />
            Ne te laisse pas mourir lentement<br />
            Ne te prive pas d'être heureux
          </p>
        </blockquote>
        <figcaption><author>Pablo Neruda</author></figcaption>
      </figure>
    </wrapper-container>
    <wrapper-container class="text-container" name="eloge-funebre">
      <h3>Eloge funèbre</h3>
      <figure>
        <blockquote>
          <p>Papa,</p>
          <p>
            Toi et moi, nous n'avons jamais beaucoup parlé ensemble. Il faut dire que tu n'étais
            pas, et c'est peu le dire, un grand bavard… Préférant exprimer ta sensibilité à travers
            tes sculptures, tes dessins et, surtout, ton regard tendre…
          </p>
          <p>
            Et pourtant, malgré cette économie de mots, de par la personne que tu étais, tu m'as
            tant appris…
          </p>
          <p>
            Tu m'as appris à suivre ses passions, qu'elles sont une richesse bien plus grande que
            tout l'or du monde.
          </p>
          <p>
            Tu m'as appris que le travail était une vertu, toi qui, bien après l'âge de la pension,
            rentrais encore à la maison tard dans la soirée puis te mettait à ta grande table à
            dessin en rentrant, te plongeant dans des schémas hydrauliques complexes (auxquels je
            n'ai jamais rien compris)…
          </p>
          <p>
            Tu m'as appris que l'amitié était un trésor, et qu'il fallait chérir ses amis. Avec ta
            grande bande de potes, hétéroclites au possible, mélangeant artistes, techniciens,
            ingénieurs, un dentiste et même, qui l'aurait cru, toi l'anarchiste, un juge !
          </p>
          <p>
            Tu m'as appris que l'argent ne valait rien en comparaison des valeurs telles que
            l'intégrité, l'amitié, la loyauté, la fidélité...
          </p>
          <p>
            Tu m'as appris que la tendresse était plus importante que tout. Comme tu aimais souvent
            le répéter :
            <q>Ni dieux, ni maîtres, juste la tendresse</q>.
          </p>
          <p>
            Avec toi, les vacances devenaient des aventures, et ce n'est pas Cathy qui me
            contredira. Au volant de ta Jeep, ton fameux foulard rouge autour du cou, à sillonner
            les pistes, armé d'un bâton ramassé en bord de route à grimper les montagnes, d'un sac
            de couchage pour dormir sur une plage en Sicile...
          </p>
          <p>
            Ta vie a été riche. Ou plutôt, devrai-je dire tes vies : car tu en as eu mille, ou
            presque.
          </p>
          <p>
            Enfant ayant grandi dans une ferme pendant la guerre, tu deviens indépendant dans la
            mécanique automobile, que tu avais apprise auprès de ton papa, que tu chérissais.
          </p>
          <p>
            Précurseur dans l'âme, tu t'aventures à triturer les moteurs diesels, à une époque où on
            pense cette technologie vouée à mourir dans l'œuf.
          </p>
          <p>
            Quand la monotonie a commencé à s'installer, tu t'es lancé dans l'hydraulique, apprenant
            en autodidacte via des cours en correspondance.
          </p>
          <p>
            Entrepreneur, tu fondes les ateliers Schoonbroodt Hydraulics, rue de l'Epargne, qui
            existent toujours aujourd'hui, avec aux manettes ton fils spirituel, Michel.
          </p>
          <p>
            Et, quand tu as estimé avoir fait le tour du sujet (et surtout, on ne va pas se mentir,
            quand tu en as eu marre de la marée de papier qui t'envahissait, toi pour qui le travail
            administratif s'apparentait aux 12 travaux d'Hercules), tu n'as pas hésité à tout lâcher
            pour accompagner ton fils Djo dans le sud de la France en Aveyron, y élever des chèvres
            et vivre en quasi-autarcie à La Broutie, ce petit coin de paradis perdu…
          </p>
          <p>
            Cette région, tu en es tombé amoureux, et il y avait depuis un peu d'un agriculteur
            Aveyronnais en toi, mais sans le côté pingre…
          </p>
          <p>
            Rentré en Belgique, contraint et forcé par une Polonaise au caractère bien trempé, tu
            t'es également découvert une passion pour la sculpture, et j'aimais te voir dans ton
            atelier, sublimer la terre, à l'occasion un bout de cigare, piqué à maman, au coin des
            lèvres.
          </p>
          <p>
            Ces dernières années, elles ont été bien difficiles : marquées par les pertes et le
            handicap…
          </p>
          <p>
            La perte de Thierry, ton magistrat préféré (que j'aimais appeler “Votre honneur” pour le
            taquiner, ce qui ne manquait pas de te faire sourire).
          </p>
          <p>La mort de ton fils Djo, que tu adorais, emporté par le cancer.</p>
          <p>
            Et, récemment, la perte de ton copain Jean-Claude, qui a décidé de jouer, avec la
            flamboyance qu'on lui connaît, son dernier acte cette année.
          </p>
          <p>Et les autres…</p>
          <p>
            Le handicap t'a laissé diminué. Toi qui as toujours été si fort, grand marcheur aux si
            beaux mollets, tu ne tenais plus sur tes quilles. Cette dégringolade, te laissant alité,
            a été d'autant difficile à voir qu'on connaissait tous ton indépendance et ton
            infatigable soif d'aventure et de projets en tout genre. Ce n'était plus une vie…
          </p>
          <p>
            Mais, jusqu'au bout, tu auras eu la chance d'être bien entouré. Par ta petite Jojo
            chérie, qui a admirablement veillé sur toi, par tes enfants et petits-enfants, par tes
            amis. Tu voulais mourir à Desnié, loin des hôpitaux et des maisons de repos, que tu ne
            savais pas voir en peinture. Et ta volonté a été exaucée. Tu es parti apaisé, en
            compagnie de ta Jojo, tes enfants (légitimes et spirituels), une petite boule de poil
            rousse à tes pieds.
          </p>
          <p>
            Aujourd'hui, nous avons tous une pensée pour toi. Et, pour ma part, et je sais ne pas
            être le seul, j'aurai toujours une pensée pour toi.
          </p>
        </blockquote>
        <figcaption><author>Jonathan Schoonbroodt</author></figcaption>
      </figure>
    </wrapper-container>
    <!-- 
    <wrapper-container class="text-container" name="quelques-mots-de-cathy">
      <h3>Quelques mots de Cathy</h3>
      <figure>
        <blockquote>
          <p>
            Lorem ipsum, dolor sit amet consectetur adipisicing elit. Commodi voluptatum nobis nihil
            officia reprehenderit magni maiores praesentium recusandae quibusdam nemo quia ducimus
            unde nam quam autem, dolores, ea dolorem nisi.
          </p>
        </blockquote>
        <figcaption><author>Marie-Catherine Schoonbroodt</author></figcaption>
      </figure>
    </wrapper-container>
    <wrapper-container class="text-container" name="quelques-mots-de-philippe">
      <h3>Quelques mots de Philippe</h3>
      <figure>
        <blockquote>
          <p>
            Lorem ipsum, dolor sit amet consectetur adipisicing elit. Commodi voluptatum nobis nihil
            officia reprehenderit magni maiores praesentium recusandae quibusdam nemo quia ducimus
            unde nam quam autem, dolores, ea dolorem nisi.
          </p>
        </blockquote>
        <figcaption><author>Philippe Schoonbroodt</author></figcaption>
      </figure>
    </wrapper-container> 
    -->
    <wrapper-container class="text-container" name="quelques-mots-de-andre">
      <h3>Quelques mots de André</h3>
      <figure>
        <blockquote>
          <p>Nestor,</p>
          <p>
            En ce jeudi 18 septembre 2025, pour beaucoup de tes amis, une page a été tournée, on
            n'aura plus le plaisir de converser avec toi, recevoir tes conseils avisés sur de
            nombreux sujets, échanger sur les voyages, les activités de la vie etc…
          </p>
          <p>
            Au début, alors que j'avais 14–15 ans, et dans la situation familiale qui était la
            mienne, j'ai trouvé un guide, qui m'a pris sous sa protection et a fait de moi un homme,
            et j'ai toujours voulu être digne de ta confiance.
          </p>
          <p>Toujours disponible pour tes amis, tu étais à l'écoute de chacun pour les aider.</p>
          <p>
            Pour ma part, je garde le souvenir de plus de 60 ans d'amitiés, des voyages au bout du
            monde et de la période à la Broutie.
          </p>
          <p>Alors, Totor, je te souhaite un repos plein de beaux rêves.</p>
        </blockquote>
        <figcaption><author>André « Dédé » Loubelle</author></figcaption>
      </figure>
    </wrapper-container>
  </texts-container>
  <!-- photos -->
  <pictures-container id="photos">
    <wrapper-container>
      <h2>Photos</h2>
    </wrapper-container>
    <!-- svelte-ignore a11y_no_static_element_interactions -->
    <!-- svelte-ignore a11y_click_events_have_key_events -->
    <gallery-container bind:this={galleryContainer} onclick={(event) => reorderImage(event)}>
      <img src="images/nestor-1.jpg" alt="Nestor Schoonbroodt" loading="lazy" />
      <img src="images/nestor-2.jpg" alt="Nestor Schoonbroodt" loading="lazy" />
      <img src="images/nestor-3.jpg" alt="Nestor Schoonbroodt" loading="lazy" />
      <img src="images/nestor-4.jpg" alt="Nestor Schoonbroodt" loading="lazy" />
      <img src="images/nestor-5.jpg" alt="Nestor Schoonbroodt" loading="lazy" />
      <img src="images/nestor-6.jpg" alt="Nestor Schoonbroodt" loading="lazy" />
      <img src="images/nestor-7.jpg" alt="Nestor Schoonbroodt" loading="lazy" />
      <img src="images/nestor-8.jpg" alt="Nestor Schoonbroodt" loading="lazy" />
      <img src="images/nestor-9.jpg" alt="Nestor Schoonbroodt" loading="lazy" />
      <img src="images/nestor-10.jpg" alt="Nestor Schoonbroodt" loading="lazy" />
      <img src="images/nestor-11.jpg" alt="Nestor Schoonbroodt" loading="lazy" />
      <img src="images/nestor-12.jpg" alt="Nestor Schoonbroodt" loading="lazy" />
      <img src="images/nestor-13.jpg" alt="Nestor Schoonbroodt" loading="lazy" />
      <img src="images/nestor-14.jpg" alt="Nestor Schoonbroodt" loading="lazy" />
      <img src="images/nestor-15.jpg" alt="Nestor Schoonbroodt" loading="lazy" />
      <img src="images/nestor-16.jpg" alt="Nestor Schoonbroodt" loading="lazy" />
      <img src="images/nestor-17.jpg" alt="Nestor Schoonbroodt" loading="lazy" />
    </gallery-container>
    <button onclick={() => showNextImageInQueue()}>Photo suivante</button>
  </pictures-container>
  <!-- remerciements -->
  <thanks-container id="remerciements">
    <wrapper-container>
      <h2>Remerciements</h2>
      <p>
        Nous tenons à exprimer notre profonde gratitude à tous ceux qui nous ont soutenus et
        réconfortés durant cette période difficile. Votre présence a été d'un grand réconfort pour
        notre famille.
      </p>
      <p>
        Un merci tout particulier au personnel soignant, gardes-malades et aux médecins qui ont pris
        soin de Nestor.
      </p>
      <p>Avec toute notre reconnaissance,</p>
      <author>La famille Schoonbroodt</author>
    </wrapper-container>
  </thanks-container>
</main>

<style>
  nav {
    width: fit-content;
    display: flex;
    align-items: center;
    position: absolute;
    top: 1em;
    margin: auto;
    left: 0;
    right: 0;
    z-index: 999;

    @media (min-width: 640px) {
      left: auto;
      right: 1em;
    }

    ul,
    li {
      list-style: none;
      margin: 0;
    }

    ul {
      display: flex;
      gap: 1em;
      padding: 0 1em;
      margin: 0;
    }

    a {
      text-decoration: none;
      color: white;
      font-weight: bold;
    }
  }

  header {
    position: relative;
    width: 100%;
    height: auto;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    text-align: center;
    background-color: #0f0f0f;
    color: white;
    z-index: 100;
    padding: 3em 1em 4em;
    box-sizing: border-box;
  }

  header blockquote {
    position: relative;
    text-align: center;

    &::before {
      position: absolute;
      left: -0.5em;
      content: '“';
      font-size: 4em;
      line-height: 1;
      vertical-align: -0.4em;
      margin-right: 0.1em;
    }
  }

  header video {
    position: absolute;
    width: 100%;
    height: 100%;
    object-fit: cover;
    opacity: 0.1;
    z-index: -1;
  }

  header :global(enhanced\:img) {
    border-radius: 8px;
    margin-top: 1em;
  }

  wrapper-container {
    display: block;
    max-width: 640px;
    width: 100%;
  }

  .introduction {
    font-style: italic;
  }

  texts-container,
  pictures-container,
  thanks-container {
    display: flex;
    flex-flow: column;
    justify-content: center;
    align-items: center;
    margin: 2em auto;
    padding: 0 1em;
    position: relative;
  }

  texts-container {
    wrapper-container:first-child {
      margin-bottom: 2em;
    }

    .text-container {
      display: none;

      &.active {
        display: block;
      }

      h3,
      author {
        animation: fadeIn 0.32s ease-in;
      }

      p {
        animation: fadeIn 0.4s ease-in;
      }
    }

    figure,
    blockquote {
      margin: 0;
      padding: 0;
    }

    text-selector-container {
      max-width: 1280px;
      width: 100%;
      position: sticky;
      top: -0.5em;
      margin-bottom: 2em;
      background-color: white;
      z-index: 50;

      ul {
        display: flex;
        flex-flow: column;
        gap: 1em;
        padding: 0;
        justify-content: center;
        border-top: 1px solid #ccc;
        border-bottom: 1px solid #ccc;
        padding: 0.5em 0;
        margin: 0;

        @media (min-width: 640px) {
          flex-flow: row;
        }
      }

      ul,
      li {
        list-style: none;
      }

      button {
        all: unset;
        border-radius: 4px;
        cursor: pointer;
        font-weight: 500;

        &.active {
          text-decoration: underline;
          font-weight: 700;
        }
      }
    }
  }

  pictures-container {
    position: relative;
    overflow-x: hidden;

    gallery-container {
      width: 100%;
      display: flex;
      justify-content: center;
      align-items: center;
      margin: auto;
      overflow-x: hidden;
    }

    img {
      position: absolute;
      max-width: 100%;
      height: auto;
      max-height: 780px;
      display: block;
      height: auto;
      left: 0;
      right: 0;
      margin: auto;
    }

    button {
      appearance: none;
      border: none;
      background: none;
      font-style: italic;
      cursor: pointer;
      z-index: 999;

      &::after {
        display: inline-block;
        content: '→';
        margin-left: 0.5em;
      }

      &:hover::after {
        animation: slideRightAndLeft 3s ease-in-out infinite;
      }
    }
  }
</style>

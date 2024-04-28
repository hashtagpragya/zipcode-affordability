<script>
    import InterConnected from "./InterConnected.svelte";
import PolicyGraphLayout from "./PolicyGraphLayout.svelte";
    import ZipCodeAffordability from "./ZipCodeAffordability.svelte";
</script>

<div class="grid-container">
    <div class="header">
        <h2>Housing Affordability & Policy Implications</h2>
        <p>Team Robin Hood: Alex, Esther, Pragya, Sonia </p>
    </div>
    <div class="comicContainer">
        <div class="comicNar">  <img src="Image1.jpeg" alt="datacomic_1" />
        </div>
        <div class="comic">     <img src="Image2.jpeg" alt="datacomic_1" />
        </div>
    </div>

    <div class="comicContainer" style="grid-template-columns: 1fr;">
        <div class="comicNar">  <img src="Image3.jpeg" alt="datacomic_1" />
        </div>
        <!-- <div class="comic">     <img src="Image4.jpeg" alt="datacomic_1" />
        </div> -->
    </div>

    <div class="comicContainer">
        <div class="comicNar">  <img src="Image4.jpeg" alt="datacomic_1" />
        </div>
        <div class="comic">     <img src="Image5.jpeg" alt="datacomic_1" />
        </div>
    </div>

    <div class="graphContainer">
        <div class="graphNar">GraphNar1</div>
        <div class="graph"><ZipCodeAffordability/></div>
    </div>

    <div class="comicContainer">
        <div class="comicNar">ComicNar2</div>
        <div class="comic">Comic2</div>
    </div>
 
    <div class="graphContainer">
        <div class="graphNar">GraphNar2</div>
        <div class="graph"><InterConnected/></div>
    </div>

    <div class="comicContainer" style ="height: 500px">
        <div class="comicNar">ComicNar3</div>
        <div class="comic"></div>     
    </div>

    <div class="graphContainer">
        <div class="graphNar">GraphNar3</div>
        <div class="graph"><PolicyGraphLayout/></div>
    </div>

    <div class="comicContainer">
        <div class="comicNar">ComicNar4</div>
        <div class="comic">Comic4</div>
    </div>


    <div class="header footer">
        <p><b>Acknowledgements</b></p>
        <p> This project was developed with guidance and feedback from the <a href = "https://www.mapc.org/">Metropolitan Area Planning Commission (MAPC)</a> . This website is created as part of Massachusetts Institute of Technology's Interactive Data Visualization & Society (Spring 2024) coursework. We would like to express thanks to the instructors Arvind Satyanarayan and Catherine D'Ignazio as well as the course staff and our classmates for valuable comments and suggestions.</p>
    </div>
</div>

<style>

img {
    width: 100%;
}
.grid-container {
    display: flex;
    flex-direction: column;
    gap: 1rem;
}

.graphContainer {
    display: grid;
    grid-template-columns: 1fr 2fr;
    gap: 1rem;
    height: 500;
}

.comicContainer {
    display: grid;
    grid-template-columns: 1fr 2fr;
    gap: 1rem;
}

/* .comicNar {
    border: 1px solid black;
}

.comic {
    border: 1px solid black;
}

.graphNar {
    border: 1px solid black;
}
.graph {
    border: 1px solid black;
} */

.header {
 
 background-color: #f3dfe8 ;     
 text-align: center;
 padding: 20px;
 margin: 200;

}

.footer {
    background-color: #fff9d7 ;     

}

</style>
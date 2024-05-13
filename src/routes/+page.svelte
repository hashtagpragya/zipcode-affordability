<script>
  import InterConnected from "./InterConnected.svelte";
  import PolicyGraphLayout from "./PolicyGraphLayout.svelte";
  import ZipCodeAffordability from "./ZipCodeAffordability.svelte";
</script>

<div class="grid-container">
  <div class="header">
    <h2>Housing Affordability & Policy Implications</h2>
    <p>Team Robin Hood: Alex, Esther, Pragya, Sonia</p>
  </div>
  <div class="comicContainer">
    <div class="comic"><img src="Image2_2.jpeg" alt="datacomic_1" /></div>
    <div class="comicNar"><img src="Image3_2.jpeg" alt="datacomic_1" /></div>
  </div>

  <div class="comicContainer">
    <div class="comicNar"><img src="Image4_2.jpeg" alt="datacomic_1" /></div>
    <div class="comic"><img src="Image5_2.jpeg" alt="datacomic_1" /></div>
  </div>

  <div class="graphContainer" style="grid-template-columns: 1fr;">
    <div class="graph">
      <h2>Input Your Annual Income to Discover Affordable Housing Options</h2>
      <ZipCodeAffordability />
    </div>
  </div>

  <div class="comicContainer" style="grid-template-columns: 1fr 1fr;">
    <div class="comicNar"><img src="Image6_2.jpeg" alt="datacomic_1" /></div>
    <div class="comic"><img src="Image7_2.jpeg" alt="datacomic_1" /></div>
  </div>

  <div class="graphContainer">
    <div class="graphNar"><img src="Image8_2.jpeg" alt="datacomic_1" /></div>
    <div class="graph" style="margin-bottom: 40px;">
      <h2>Visual Depiction of the Growth of Unaffordable Housing in Boston</h2>
      <InterConnected />
    </div>
  </div>

  <div class="comicContainer" style="height: 500px">
    <div class="comicNar">
      <h2>
        As we can see on the right, as more unaffordable housing pops up in
        Boston, the cost of affordable housing increases as well.
      </h2>
    </div>
    <div class="comic"></div>
  </div>

  <div class="comicContainer" style="grid-template-columns: 1fr 1fr;">
    <div class="comicNar"><img src="Image9_2.jpeg" alt="datacomic_1" /></div>
    <div class="comic"><img src="Image10_2.jpeg" alt="datacomic_1" /></div>
  </div>

  <div class="comicContainer" style="grid-template-columns: 1fr 1fr;">
    <div class="comicNar"><img src="Image11_2.jpeg" alt="datacomic_1" /></div>
    <div class="comic"><img src="Image12_2.jpeg" alt="datacomic_1" /></div>
  </div>

  <div class="graphContainer" style="grid-template-columns: 1fr;">
    <div class="graph">
      <h2>
        <center
          >Graphical Representation of Housing Costs Across Sales Years</center
        >
      </h2>
      <p>
        <i
          ><center
            >Using Brushstrokes to Explore the Potential Allocation Increase for
            Boston's Affordable Housing Fund: Estimating Homes that could be
            Built</center
          ></i
        >
      </p>
      <PolicyGraphLayout />
    </div>
  </div>

  <div class="Summary">
    <h1>Summary</h1>
    <p>
      Housing unaffordability in the Boston area is on the rise, posing
      significant challenges for residents who can no longer afford housing that
      costs less than 30% of their income. This trend is fueled by several
      factors, including an increase in corporate ownership and the growing
      market for high-end luxury properties. To address this pressing issue, we
      propose implementing two key policies: transfer fees and a progressive
      tax.
    </p>
    <p>
      The first policy, transfer fees, would impose a levy on real estate
      transactions, particularly targeting high-value properties. This measure
      aims to capture a portion of the wealth generated from the buying and
      selling of luxury real estate, redirecting it towards initiatives aimed at
      increasing affordable housing options.
    </p>
    <p>
      The second policy, a progressive tax, would target individuals or
      corporations with higher incomes and property holdings. By implementing a
      tax structure that scales based on income and property value, we can
      ensure that those who can afford to contribute more towards addressing
      housing unaffordability bear a larger share of the burden. The revenue
      generated from this tax can be earmarked for funding initiatives to
      develop and maintain affordable housing units for low to middle-income
      individuals and families.
    </p>
    <p>
      By implementing these policies, we can address the root causes of housing
      unaffordability in the Boston area while simultaneously redistributing
      wealth to support those most in need. Investing in affordable housing
      initiatives not only provides shelter for those struggling to make ends
      meet but also fosters economic stability and social equity within our
      communities. It's time to take decisive action to ensure that everyone has
      access to safe, affordable housing in the Boston area.
    </p>
  </div>

  <div class="header footer">
    <p><b>Acknowledgements</b></p>
    <p>
      This project was developed with guidance and feedback from the <a
        href="https://www.mapc.org/"
        >Metropolitan Area Planning Commission (MAPC)</a
      > . This website is created as part of Massachusetts Institute of Technology's
      Interactive Data Visualization & Society (Spring 2024) coursework. We would
      like to express thanks to the instructors Arvind Satyanarayan and Catherine
      D'Ignazio as well as the course staff and our classmates for valuable comments
      and suggestions.
    </p>
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
    background-color: #f3dfe8;
    text-align: center;
    padding: 20px;
    margin: 200;
  }

  .footer {
    background-color: #fff9d7;
  }
</style>

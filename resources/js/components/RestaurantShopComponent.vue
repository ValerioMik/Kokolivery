<template>
  <div v-if="dishes.length" id="shop">
    <div id="dashboard_box" class="row">
      <div class="col-xs-12 col-md-3" id="dashboard_img">
        <img class="img-responsive" :src="`../../storage/${restaurantData.image}`" />
      </div>
      <div class="col-xs-12 col-md-9" id="dashboard_cont">
        <h1>{{ restaurantData.brand_name }}</h1>
        <h3>{{ restaurantData.description }}</h3>
        <h3>Valutazione: <strong>{{ restaurantData.rating }}</strong> ({{ restaurantData.num_rating }})</h3>
        <h4>{{ restaurantData.address }}</h4>
        <h5>Minimo ordine: €{{ restaurantData.order_min }}</h5>
        <h5>Consegna a €{{ restaurantData.delivery_price }}</h5>
      </div>
    </div>

    <div id="categories_box" class="row-md d-flex justify-content-center m-5">
      <a
        class="col-xs-4 col-md-2 btn btn-orange mx-1"
        v-for="(category, i) in arrayCategories"
        :key="i"
        :href="`#${category}` + `link`">

          {{ category }}

      </a>
    </div>

    <div id="content_box" class="row position-relative">
      <!-- dish cards -->
      <div id="dishes_box" class="col-xs-12 col-md-8 d-flex flex-column align-items-around">
        <div
          class="category_box"
          v-for="(category, i) in arrayCategories"
          :key="i"
          :class="{
            ant: category == 'Antipasto',
            pri: category == 'Primi Piatti',
            sec: category == 'Secondi Piatti',
            piz: category == 'Pizza',
            des: category == 'Dessert',
            bib: category == 'Bibite',
          }"
        >
          <h2 class="col-12" :id="`${category}` + `link`">{{ category }}:</h2>
          {{ setCat(category) }}
          <div
            class="dish_box col-xs-12 col-lg-6"
            v-for="(dish, k) in arrayCategoryDishes"
            :key="k">
              <div class="dish_card row p-3">

                <!-- <img :src="require(`../public/storage/${dish.image_path}`)" alt=""> -->
                <div class="col-9">

                  <h2>{{ dish.name }}</h2>
                  <h5>{{ dish.description }}</h5>
                  <h3>€{{ dish.price }}</h3>
                  <br>

                  <span
                    class="btn btn-orange"
                    v-if="cart.some((item) => item.id === dish.id)"
                    @click="addToCart(dish)"
                  >
                    +
                  </span>

                  <span class="btn btn-orange" v-else @click="addToCart(dish)">
                    Aggiungi al carrello
                  </span>

                  <span
                    class="btn btn-secondary"
                    @click="removeFromCart(dish)"
                    v-if="cart.some((item) => item.id === dish.id)"
                  >
                    -
                  </span>

                </div>

                <div class="col-3 img-responsive d-flex align-items-center">

                  <img
                    src="data:image/jpeg;base64,/9j/4AAQSkZJRgABAQAAAQABAAD/2wCEAAoHCBYWFRgVFRUYGBgYGRoaGBgYGhkYGBgYGBgaGhoYGBocIy4lHB4rIRgYJjgmKy8xNTU1GiQ7QDs0Py40NTEBDAwMEA8QHhISHjQrJCs0NDQ0NDQ0NDQ0NDQ0NDQ0NDQ0NDQ0NDQ0NDQ0NDQ0NDQ0NDQ0NDQ0NDQ0NDQ0NDQ0NP/AABEIALgBEgMBIgACEQEDEQH/xAAbAAACAwEBAQAAAAAAAAAAAAADBAACBQEGB//EADcQAAICAAUCBAQFBAEEAwAAAAECABEDBBIhMUFRBSJhgRMycZFCobHB8AYUUtHhYnKC8RUzwv/EABgBAAMBAQAAAAAAAAAAAAAAAAABAgME/8QAIREAAwEAAgMBAQEBAQAAAAAAAAECESExAxJBUSJhkRP/2gAMAwEAAhEDEQA/APjyrLqssqwiiIZULLhZ0CSGgSoRELGgCYzlskW3awO3U/6mng4AA2FCLR4IYPh/+R9h/uO4eABwAIwqS+mQ2GgkSDzONoW6J+kcVJY4YPIuToAMM2a60DX1hdEu+BdUBfWxfEOuHE2AtoMjoK83H3ji4fpCf25PSLRmc2Ej+YU1dz+U7hkMLW6+wmgMntVCcGSo7bDt0+sr2f4PWJFJzRH/AO1MGcA9pPIhQpFMfMMA2kWR+nv+01fhwD5UE3uD/OkaaXY1n0yMDMuxtgaXmhR9weRv07R9kkHh3mB1bLwKo36sOYz8OOqXwdZ8EtMoUjbJKukSoQg2CPX6wL4R+00FIPFGVKS1f6GJmYRKFY+2CDfQ/lAPh10miektYKFZQpGtMoVjEJskoyxpklGSACTpKFdo2yQKryPeAANEkZ+GJIAQCWElQmDhFzQ+/QfWIZVELGlFmamUygXc0W/IfSFy2WCChfqepjKCQ6DoiLB5zF0L1s8VzcYUQeZwS+1Aix/DJT18jXfItl87YvtyoBJI6kGaWAwZQw6xbAyl/h0jir5HqBNDAwQAFUUBwBCmvg6z4RVhsPDvpGcHK3zHsLLgRKWyRHDypjSZP0jigCRsQAWSAB1OwlKUgArlhOuiqCTQA5J2A+szMx/UuCraVLO3/QAR9QxIBH0uI5zxBsT0TovU+rVz9OJN+RQgw08fxLDBAU6yf8aIH1Mvl8cOLAo9Qef/AFMbKZfrx6xxaXdSCwBIF/kamE+dt89Dawea4NiZfDJZVJ5IBM4RNnQ0gWvuIs+aQ6T+BiVZuGRuBY+t/lDZtCQGX5kNj1HUfb9Iriqvz1aP5XHZjsD78fWu8WlYWwLJOGx868How6MP537GWfDI5ETKMDou3TdCPxp2Hrtt6gji4/ls4HG/PX/cWBgFkgcbDsEcXH3wQd1MVxcKwVO1ij39ouhGblcsqFtK0L3JayTz7Q7LGRhAf76mu8q6Q3RYKMggyg4Pt6RspBsspU0MzsXDr1B6/wA4gmT0mjXIIsGLYmFXqDwf2+s3mtJqcFGWDZY0ywZWUSKssXxNiD/KjxWBxE2gBT4f0kg9TTkAO4WGWND/ANDvNrLZcKKHue8HlMsEHqeT+0bWukzpldHal1E4BCosgCKIQSqiMYSX9IgO4KEzSy+BUpgoPaFbFlqUuWAcMBOHGipe5ZWkuhqQPivifwEDlS1sFoGubPPtPHZ/NPiks7EKel+UV0A/eeq8dwA2CSRekgj06fvPM51yyIxUClCiuoXbV78yfYbRzI5VdKsBybs78dKPSbuOqihpVdwduDYFe/eKf0/hXh/+R9t5rPl9xVbUb5o9zOTy03TTFgfBUaaBHM5g4Y3IAHqOT23lPjkh2O5Bs1tsd7AGwF9ITLuNOq/Ka5IsbGtul1JnNJYIZlkLIig0ocX1BF1zzLeHtjMW+KiqvK0d/fczGy3iYfFIDUbAA22BHA/OarZNwQMFyvmBrppN3XejQmypy8Y0POtTOOGFYqd1cHb06r7cj0vtF8t4u2rQ5sg0bHbYx7HUMtqfUHsRNPZPj6aIz8a9kPzobRv8hzV+oHsV7cz4gBDj5X56aXJ3sdATd9jfc0TSWTWK1ox27dShPauv0MDiuo8/OE+zg/gbgk/ofp6br21YVmD6sRuPcQoYOKPMxlzRQhWNjbS3JZTwrevAB6/XnWVDQLDST0JlJU0TXqgboQalHSPILFGLYmGVNGJiwWZYu7m/lNb2Tt9KjGAja2BqiBXa+u32gs4rkWm9XqUiw3pKlaOZ0oLIF7enaDGwII29vYj1lcPXRGkAgbEG1nMDBcLTtqPeUv5YNZwAxUo17g9CO8EVj9BhpPI+X69voYmy9JvL0ypYwDJ6QTrGmECyxiFfgzkZ0zkANBkBBHf+cxZh8MEpZ735v51jTqTxftX7zmHhBgysvNX6+4mKf6XObyMYJsBr69drE6jg8ETmM6JVgHsOTt1qdw8AWWoWT2hTHTGMNLj2En2gsHDqFdugjUpLWT/hfExfYdIMNKcywmVUaTJ3VCIZSIHxBtLtpoA0hJ+b1qZui8GPGmJwGANE1+syvEMqVRBXQD7CLa2dtTX352HQD7zQy+OcZAOVRqHO9iv9SW3mmdPngyvDsdkZio1Dby/rXrN7J55S63dHYg0DdA8HkVfEwcYlMU7WCaP/AE+v03juJ4cuMAVcq1ghhuO38ox3M09f/Se1weoTCW9RHTitv+YDGIRQSwCgGzQOnTuK9fSZGXzuYwfLiqzoPlfm+OXH/wCt5Zc+rsqhNbE7Kflvc3XHHWZ+mMnDFwsk4IdTtrJDVRbf5gDwNxPbeHs3wSbGpeSfwgbm/aEyuSoh8StN0xFDerVALFDr+st4G4xPiDUDZdSQKAJs8e9RXTpplZ8PK+K5XBXzhXZsRjpcE2Gu9C7VQrr0uP8AhaYq+RiDsDtvYIHParo+sNkCuMgU8qfJYHkcbH/X0Mr4bjhVNqyvbK+vdbsDYezCpfs6XPaE9kq+2KSgIHDg8Mo/ED3F7fUiKZnLMrAqVAcWw5G50kr2YAe/6emyiYZDINywB8w3ahuR6QWZ8AXE4Yow4I3HeiJp41/Sb6K9qc8GFl3XDBCbXvvvXoOw9JDmGbYnaHx/6ZzCnZ0YepYftKDwfHA3fDH01H86E7k0czmmzmDjkGvcfv8At95ohg6+sxMymIikMisfwuCRR/YwmRzhTSHZd+x3v6dZzeXN1HT4/ZrGMZkHYAlTxt07mdwlIFEk+vWNYwBphuDAOtTLWilwwbQLQ5EG8egxPEEmZUGnHXZq6NX7/wC4Z1lcsASVP4tvoeh+/wC818dfCWtQiwgnEYdKPYjkQTidBkD0yS+iSAxwCFRZwCEQTmZRX4Yu65jeBh7wCiaOAm0FywOrtBNCuJQCO64wqV9OATqpLATtTlpm6JpmX4umoqoOkEV9L5musyvGhWk3XPsBzJXLCujLzBVPlNqzKN/Q1X3ubHg2XYghVrU/UcbDmp5jPMVIrhdx6Ed++82XzeJpVsNyhIDbMaugfeaVOyjDRDP5gHHxFo2Ha77BqP5xnLEjdD7dO+0z87hMx+IluzXrFXuTZNjrcNlXOkHqB5h7c/eVUr14JTNlPFGVSKN/ziav9PIjEu9BvxGgCF5/PvMrAxExKB2NbEX+Z95bFD4TjWGKsKDDjb/QHEwa+FNmt/UfiiuhRUVgtgBQ1C7qiOSL5P6RH+j8cqzI1GipA6DUCCNvaMYGGtrsdD7gA/MOCBXBsRTwHKFM4yOKBGoVyBrFbnrRlPmWiU+TQzSJhZghAAr6mIAoK13X2MaKI4cqtl6DmxyN1IWtzdCU/qXLMFoMzOul+ANQuzrB3HUfU8naE8AAILc2NpNLlP8AQ3VgtnNOHp/CSw0dwa4/K/tNPAzgNUV36A2dufziHjNHDOJVkEnYWfIDwB9Jg+AYmGu7uACb3sFifNd11u6E1jVOhLx4e2xHuLOlzqOCAQbBGx9Jxmm800W50zc5hbVMRsFVayoI79V+vpPRZkzGxxvJtew5eC2Jm1Q6VIs15ehvv24P2hsq+KyO7oAiHzOCAoBNDk7n0B6GZGY0oQzorUw0dNt9r7TuY8QOOUVm04Y+VENXdg1sKXkFj5j+UqYSlImqbem8UHvBOszsfNuCNNoNhprVwQAbaybo+7Cq4jWVzYelag/UdCetSHODOnaK4nP1j+IvtE8ZYS+Rornlun/yFn/uXZv2PvFWXaOhtWGw/wACGH0Plb89MVI7zql6jKljBaJJ2SUIfJrc/wAs1CLFsFAfNbEHem6b9LjGDhAEkctyTOVlJjGGLIE0cJIll13mki7SpQMVxZRRC4g3lUEijSToWWCzpNCz0iH/AMohHlN871Q+pvpMKNNwfExPHsx5kQHmvuT/AD7wmJmHKNqYDoCvlO/UMbmJi46sQS1Fel2TvsbP6wla9Jq+MOZnD523HPvK+BY16sI8CyvfncfofvG8cjVR2V9geln5fv8AvEskAjlaGpidz69B6bTWX/DTMn2aWko2rkd/X1lDklIZ8MlmNUlgb35tz6XDK4I3G3f9jO5ltlCAKRyQfK3tf6TNNoGJ4DFWpgcNtqDbagex4I+k2vEcR8RErZUPyA778txvxUJlsN9NuDXFGiQxHlGnqD+ktmcEoyoyEFtl07g79BxB43pOgsh4iMN702AOHBKoTyFN7WRf3jiAXhYykat1chrO45r6r+cz2wEsliVF6SSOT/jxz/uP5bwlFKujv5T5kIN2OBv9uLEVJYwXZ6DOs7BKXzuw3ABZlrcXyOVvpv04i/h2SZH2OpHuz63+XPEQ/q3xx8vgYTYYw7ZmUqwtqWjYAPFjfp8sD/RfibOnnHLswq6W2uhZsKAT7CT6NwqfQdNjXiCeT4agqxVirnemoiyTyduOxnkcl4U5cq9UTpJO4VgBVdBtW3rPYnMfER30ecMVA4tqF103vpMzGwMQEO9YaA6mBYaiQOwO8uKaWBnJvYWCERUHCgD7QbGTKYutFeiLHXmUxEmjRtLAY5mZjDeaGMIi4i0rBLMZdWHmUGu4BI+naZeHlKJrdtyK5OkUEAsdPX95uldjMbO4NMGrccHfb3HE0ivhnU/Tr4Y01sR3B79KJ53Gxu7FGDJ6VW+53vbfn8+/1+aAxs0wVgNmaqbYG/U8e8Z8RTHwt3RGVlGlgWKhup2qzzXtz1eZ9BPTTwcXUou7Ivf8Q41DuLBlcRRRimCCwViuk811s9z1MZ194nP4LSZDkofxBl9yDX51FSPT9Y1lT51PZhXse0BmF0sRfDEfY1N46JvsHtJJJLIHVhUgkaGWcpY1leZqIvlmXleZq4XE0joTE8Rd5SobFG8qBJpFyzmixUy814ahetx1IFAelzYWZ3iLrqArz8ixYA7npOev00EczlVVH02bHewCP0nlMXBrUdrF73uR23/ae1bD1AguSSOnFV2G083ncoy6rWwFsnkrvQ++0fhrGRaCZPF+IgVqDKAFr8Sgbe+0ri7kFrDLVH/IA8GI5ZiObAsfUV1H5TT/ALkHy4gojhu/1l0seolM5gMTqFnykEjgebt34h8vhFm8pII5vahW/PIlMglYgsnR+I87bH3npsqqOwOGrCvKboagf236zG6wGjNymIdSI/k0ElHok+ljrQM1P7x31a2RrIulA06aAr27zDbwtcI6f7hyVsEACtjwQd4mqYrsQigkAnUxIND6XK9U3wycNrO46XoDhmU2AG67Hcf+IO46R7KZ8Kh+M9NVqpTVYB/CasHfv/x5XI5AqdwpY3S77mue98bz1GYCoo1jzCrr5RS8E9TtwPWKvXpAzx/jFvibr8xaruwt2ABe3P5T0fhWaGBganoXSoD9CSf4JhYOe+LmNWGoCr0YbEeo6eg9I1msH472L0JSgLxquzfqT+ku11L6QHrvDsFxlEINs1uxPXU7MTt6TBzqDzI5Ys7Eqa1LzwpJ2oenSbWYdsLLAuwAC2im9wq8N7zz/gAfMN8Rmsk+RAaRe9itie+/Mjx8r2B9ns8vg6UCmvbgegg8RY63EVxBN3PBomZ+OIg4mjmTM55lSNUDAi2ZwgTGlgnFmLoliZyqnaP5RwF+FiAFel7j0BirSyODsZoq9ljIzHqCZvLadxx+n1iLJNZMTbS3sf2MRzeDp3H/AK/4hyh8MT+JpM7ncQMzsOCSRe3Jv94u533jmLihcNRpUlrLEjcC6UA9ODOjx1pFLgXsSQWsSTQzNFYZJl4+M9AilJNbkHY8GobJYj6mVyDR2r6Tnc5yW5xGtgNuJr5dpiJ3mplsTiVAmExlggIxiiBjpDlkWDxcEEgkA+vWoQQgmNTpomL4b6vkHBqyNvWor4j4NrGoHzAdeDNZAIZTJU4G6fNfEMhiITYO/pt9L4PHeEyucC0uMmoCqK76dtgwHM9/mcurqQwsGeaz/wDTp30NQ7EXK74ZLnOhRvGMLhQdW9WCFFA9yL3rYTQ/pbPB8Qq5AsAjUQAWB4A68zDbLamAZFGgm2O5NECqJF7b7doYeGEMdJseUlh0UiyLHXSdwO4EiohrCPZpm14xj4aZh1JQ2b2N7uAxuutkymFionynSONW1AGr3bYDj0nmfEVKWMMEKbBsc6Sd991vY19e8plF84LnUh8pC0AQdzRqh3+0P/JZuhp6rP53CwdB1F2bYf8A1mvXy8D2imfZ8RFdl0LXJ7E87b9ZRxrAGgDDSulltwFGquttx2qKZvFfFpV1BF2BJOprpeTsu0JlIWl/A8sqB2bYcA+pG3M3MgoVSHIF0SeKrcEjgHbn6zBzXh+OMME6216TpqyoFblhw235zS8CyuK9B9ZGvcHih/nfSttu4jct86DY1/UCnMjDwkbUd9RU/JWjy0Ls/NyRxN3+n/CRgIFJBauar+H1mqmGqilUD6CVZprEeqSKz6ccxXGMM7RPMPGykJZholiGHxXihNzGjRF1gGMM/Fd4FxE0LQDQDGMM1RNsdbq4JBozh5ogUdxDuQ60Goj5W7eh9JnN6SgxCs1l/pDX1Fq3phR4Pb7dJzMt06CgPoJcOH2/EBsfQdDBOCNj/wAj6zWZS6Jpt9gpJah3klEmbhE6l6UdLdau7M1cNtxo7jV+d79ztONvxDYCgWa3PWS2W6THlaOZXE6TNR4dG63M1wyTeR7EE4i+Wxoy+817QuioMsDBXLAyGikxhWhFaKXCoZOD0bUywQGLKYRWjxBpzF8OR71KDfMuvhmGBWkfyh+w+06rQgaPEJoz8z/TuA4+QD6bRE/0oBQRgABQBUd7s+u/PoJ6EPLB4eqYGOPANSlHYKtg+QUbHBsx3JeBYOGmii45OrezG/iSF4KZQsDoFUBQAANgPQSah0i5eVLygDM8GWgmeCfEgMvi4kzsxiS2PjRDFxJnTLlHMR5VBKCWc9BMv9KZV2sxfMKzbBtPfv8AT0hGMoGHWIQriO9HSPlNeb8X87xTGyAdg/ysOR/Os0WaCYy08ECeL4kM5gHMaQ0Xy55PpX32/S5V36Hpwf2PpLqwQAEA3ubsdduOJTMoAAynynbflWrdT9jvOmVwZU+TuuSKazJGLRlHhUeJI8OjzLBjiPDI0SR4ZHk4A/g4tTRwsWYqP3hsDMbkdpUvANdjOBoBMWW1S80QwGnVaLh5cPJaGmNK8urxUPLB5IxsPLh4qry4eMBkPOh4sHndcYDGqd+JFTiTnxYANF5VsSKtiwL40Whgy2LFcXHi+JjxZ8W5FUWkFxMWCE4JGcCR32MuWqBLSrt1Jiv90DxzfHoYZomxhngy0jn1gmaGBpZmg2aRn2gWeUkTpMQ1KYK6nAHXn6QbtCu2hCer7D/t/F/qaTOsbeIHmnJdrFb1XoNhKMaQj/Igj2qz+X5yj45JF0T3I3odIB8SyevW/wBpsZHdRkgfeSAF1eHR4gjw6PM2hjyPDI0SR4QYg69IsGht8bSLncs55Iqze8AHsDSftCYjkjbmvzg5Bo0cPFIjSY1zEy2MSNz/ALjC4kSeB2a2udGJEEzHeFXEBj3QHRiS4xYjqk+IYYM0BiTvxZnfGM78eIDQ+LO/Fmd/cThx4AaRxpRseZxxjKlzJ0rB5sxAPjxcAy1ARcjO2TLgQTYoHEE2LFgaMPi1xE3xW6qfqN5ZnuVZoC7JgYgdTZ24P24lkRVGwA9oFUXiut+8UzWaOyqaJPP0lqSkhvGxNNepqcLwD4tmvS/eDwmocEfXeS5IfYZ2gXeR3gC1xpCGMHDLGjsOT9O85msQE2dhVKo/xGw/UzuYxNCUOWNE9trr8pml5sliFT0YbHHAX7yi4iHaq7dosWg3aMkb+D6yRT4x7yQACjxhHkkksC6429SxQnqZJIFIPl0o2GJHaNK5kkiYPsvhjsPyhAT2MkkkC6sex+0uCex+0kkQBFdux+0IMRv8T9pJIwLfE9D9p2/Q/aSSGjJXoftJXoftJJEBNXofsZwuf8T9pJIgKtiN2P2lLPUGSSICtHsftOUex+0kkBlTfYyu/YySQEVN3wYrmMI2L4EkktD1lg3aUdjOyRAwDk9oQvoF15j8o7es5JLkVdC/xrXS1nqD1vvF2ruftJJLIBs/SCZpJIAc1ySSRAf/2Q=="
                    alt=""
                  />

                </div>
              </div>
          </div>
        </div>

        <div v-if="this.addMessageOpened" class="cart-message cart-add">
          "{{ selectedDish.name }}" è stato aggiunto al carrello!
        </div>
        <div v-if="this.removeMessageOpened" class="cart-message cart-remove">
          "{{ selectedDish.name }}" è stato rimosso al carrello!
        </div>
      </div>

        <!-- cart icon -->
      <div class="d-md-none" @click="isCartOpen = !isCartOpen" id="icon-cart">
        <div id="cont_icon">
          <i class="fa-solid fa-cart-shopping"></i>
          <div v-if="cart.length > 0" id="count">
            {{ quantityTotal }}
          </div>
        </div>
      </div>

      <div class="cart_position d-none d-md-inline" id="cart-box">
        <table>
          <tr>
            <th>Qnt.</th>
            <th>Piatto</th>
            <th>Prezzo</th>
          </tr>
          <tr v-for="(item, i) in cart" :key="item.id + i">
            <!-- quantità presa da dish_order -->
            <td>{{ item.quantity }}</td>
            <td>{{ item.name }}</td>
            <td>€ {{ item.price }}</td>
            <td>
              <span class="btn btn-secondary"
                @click="removeFromCart(item)">
                -
              </span>
            </td>
          </tr>
          <tr>
            <th></th>
            <th>Totale</th>
            <th>
              <b>€ {{ cartTotal }}</b>
            </th>
          </tr>
        </table>
        <a :class="!cart.length ? 'disabled' : ''" class="btn btn-orange" :href="route">Procedi con l'acquisto</a>
        <!-- @click="sendCart" -->
      </div>

    </div>

    
  </div>

  <div id="loading_screen" v-else>
    <h1>...Loading screen...</h1>
  </div>
</template>

<script>
import VueSessionStorage from "vue-sessionstorage";
Vue.use(VueSessionStorage);
Vue.config.productionTip = false;
export default {
  data() {
    return {
      url: null,
      dishes: [],
      restaurantData: "",
      arrayCategories: [],
      arrayCategoryDishes: [],
      categoryLooped: "",
      // catAntipasto: [],
      // catPrimiPiatti: [],
      // catSecondiPiatti: [],
      // catDessert: [],
      // catBibite: [],
      // catPizza: [],
      cart: [],
      isCartOpen: false,
      addMessageOpened: false,
      removeMessageOpened: false,
      selectedDish: { name: null },
    };
  },
  props: {
    route: {
      route: String,
      required: true,
    },
  },
  mounted() {
    // salvo l'url
    this.url = window.location.pathname;
    this.getDishes();
  },

  methods: {
    // chiamata axios che mi torna tutti i piatti
    async getDishes() {
      let res = await axios
        .get(`/api/view${this.url}`)
        .catch((e) => console.error(e));
      // salvo i piatti
      this.dishes = res.data["dishes"];
      this.restaurantData = res.data["restaurant"];
      // push del piatto nella rispettiva categoria
      // this.dishes.forEach(dish => {
      //   this.popolateDishCategory(dish,'Antipasto',this.catAntipasto);
      //   this.popolateDishCategory(dish,'Primi Piatti',this.catPrimiPiatti);
      //   this.popolateDishCategory(dish,'Secondi Piatti',this.catSecondiPiatti);
      //   this.popolateDishCategory(dish,'Dessert',this.catDessert);
      //   this.popolateDishCategory(dish,'Bibite',this.catBibite);
      //   this.popolateDishCategory(dish,'Pizza',this.catPizza);
      // });
      // console.log(this.catAntipasto,this.catPrimiPiatti,this.catSecondiPiatti,this.catDessert,this.catBibite,this.catPizza);
      this.dishes.forEach((dish) => {
        if (!this.arrayCategories.includes(dish.category)) {
          this.arrayCategories.push(dish.category);
        }
      });
      console.log(this.arrayCategories);
    },

    async sendCart() {
      let cart = {
        tot: this.cartTotal,
        cartDishes: this.cart,
      };

      await axios.post("stripe/save/cart", cart).catch((e) => console.error(e));
    },

    // func per aggiungere al carrello i piatti
    addToCart(item) {
      // se c'e' un allert aperto lo chiudo
      clearTimeout(this.closeRemoveMessage());
      // cerco nel carrello il piatto che ho cliccato
      const piatto = this.cart.find((dish) => dish.id == item.id);

      // Se la ricerca mi torna il piatto
      if (piatto) {
        // cerco nel cart la posizione del piatto
        const index = this.cart.findIndex((elem) => elem.id === piatto.id);
        // aggiungo 1 alla quantita' gia' presente
        this.cart[index].quantity++;
      } else {
        // altrimenti pusho nel cart un nuovo oggetto con i dati di cui ho bisogno

        this.cart.push({
          id: item.id,
          name: item.name,
          quantity: 1,
          price: item.price,
        });
      }

      this.selectedDish = item;

      this.addMessageOpened = true;

      // mando il carrello aggiornato al server
      this.sendCart();
      setTimeout(() => {
        this.closeAddMessage();
      }, 1500);
    },

    // func per rimuovere dal carrello i piatti
    removeFromCart(item) {
      // se c'e' un allert aperto lo chiudo
      clearTimeout(this.closeAddMessage());
      // cerchiamo il primo piatto selezionato nel cart
      const index = this.cart.findIndex((elem) => elem.id === item.id);
      // faccio -1 all'attuale quantita' del piatto che si trova all'indice che mi sono trovato
      this.cart[index].quantity--;
      // se quantity e' uguale a 0 (false)
      if (!this.cart[index].quantity) {
        // rimuoviamo il piatto selezionato
        this.cart.splice(index, 1);
      }

      this.selectedDish = item;

      this.removeMessageOpened = true;

      // mando il carrello aggiornato al server
      this.sendCart();
      setTimeout(() => {
        this.closeRemoveMessage();
      }, 1500);
    },

    // func per chiudere l'allert dell'aggiunta del piatto
    closeAddMessage() {
      this.addMessageOpened = false;
    },

    // func per chiudere l'allert della rimozione del piatto
    closeRemoveMessage() {
      this.removeMessageOpened = false;
    },
    // category.toLowerCase().replace(/ /g, "")
    popolateDishCategory(dish, category, dishArray) {
      if (dish.category == category) {
        dishArray.push(dish);
      }
    },

    setCat(cat) {
      this.arrayCategoryDishes = [];
      this.dishes.forEach((dish) => {
        if (dish.category === cat) {
          this.arrayCategoryDishes.push(dish);
        }
      });
    },

    // checkCategory(){

    //   return this.dishes.forEach(dish => {

    //     if (this.categoryLooped === dish.category) {

    //       this.arrayCategoryDishes.push(dish);
    //     }
    //   })
    //   // console.log("piatti dentro categoria",this.arrayCategoryDishes);
    //   // return this.ArrayCategoryDishes;
    // }
  },

  computed: {
    cartTotal: function () {
      // salvo la variabile con il totale con default zero
      let total = 0;

      // faccio un foreach del carrello
      this.cart.forEach((dish) => {
        // alla variabile total sommo il valore di ogni prezzo del piatto(ad ogni iterazione)
        total += Number(dish.price * dish.quantity);
      });
      // mi faccio tornare il totale dei vari prezzi
      return Number(total).toFixed(2);
    },

    quantityTotal: function () {
      // salvo la variabile con il totale con default zero
      let total = 0;

      // faccio un foreach del carrello
      this.cart.forEach((dish) => {
        // alla variabile total sommo il valore di ogni quantita del piatto(ad ogni iterazione)
        total += dish.quantity;
      });
      console.log(total);
      // mi faccio tornare il totale delle varie quantita'
      return Number(total);
    },

    // categoryDishes: function() {

    //   console.log("categoria attuale",this.categoryLooped);

    //   this.dishes.filter(this.checkCategory());

    //   console.log("this.arrayCategoryDishes:", this.arrayCategoryDishes);

    //   return this.arrayCategoryDishes;
    // }
  },
};

$(function () {
  var cart = $(".cart_position");
  $(window).scroll(function () {
    var scroll = $(window).scrollTop();
    if (scroll >= 30) {
      cart.addClass("scrollEffect");
    } else {
      cart.removeClass("scrollEffect");
    }
  });
});
</script>

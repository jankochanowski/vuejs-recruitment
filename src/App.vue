<template>
  <div class="wrapper-div">
    <div class="mHeader">
      <img
        class="logoVueJs"
        src="https://jankochanowski.github.io/vuejs/logovju.png"
        alt="logo vue.js"
      >
      <br>
      <span class="logo-text">Vue.js</span>
      <br>
      <span class="header-label">Zadanie rekrutacyjne</span>
      <p>
        Domyślnie wyświetlana jest przykładowa tabela 4x4. Pod tabelą znajduje się
        <span
          class="m-highlight"
        >Panel akcji</span>. Dane w tabeli możesz edytować za pomocą przycisku
        <span class="m-highlight">Tryb edycji</span>. Aby powrócić do domyślnego widoku arkusza, wybierz
        <span class="m-highlight">Reset arkusza</span>. Aby dodać kolejny wiersz lub kolumnę, kliknij odpowiednio:
        <span
          class="m-highlight"
        >Dodaj wiersz</span> lub
        <span class="m-highlight">Dodaj kolumnę</span>.
      </p>
    </div>
    <div class="tableWrapper">
      <table id="mTable">
        <thead>
          <tr>
            <th :colspan="columns">{{tableLabel}}</th>
            <th colspan="1">
              <i class="fa fa-tachometer"></i>
            </th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(row, index) in rows" :key="index">
            <td v-for="(column, index) in columns" :key="index">
              <span class="row-span" v-bind:class="{regdisplay: regDisplay}">{{ row[column-1] }}</span>
              <input
                class="row-input"
                v-bind:class="{iseditable: isEditable}"
                v-model="row[column-1]"
              >
            </td>

            <td class="m-highlight" :key="index">{{ rowAvg[index] }}</td>
          </tr>
          <tr>
            <td class="sum-col" v-for="(column, index) in columns" :key="index">{{sum[index]}}</td>
          </tr>
        </tbody>
      </table>
    </div>

    <p class="m-actions">
      <i class="fa fa-toggle-on"></i> Panel akcji:
    </p>
    <span class="m-validation">{{validation}}</span>
    <div class="button-section">
      <div class="m-button m-spec" v-bind:class="{regdisplay: regDisplay}" @click="makeEditable">
        <i class="fa fa-edit"></i> Tryb edycji
      </div>
      <div
        class="m-button b-save"
        v-bind:class="{iseditable: isEditable}"
        @click="saveChanges"
      >Zapisz</div>
      <div class="m-button m-spec" @click="restartCreating">
        <i class="fa fa-refresh"></i> Reset arkusza
      </div>
    </div>
    <div class="button-section">
      <div class="m-button" @click="addColumn">
        <i class="fa fa-plus-circle"></i> Dodaj kolumnę
      </div>
      <div class="m-button" @click="addRow">
        <i class="fa fa-plus-circle"></i> Dodaj wiersz
      </div>
      <div class="m-button" @click="removeColumn">
        <i class="fa fa-minus-circle"></i> Usuń kolumnę
      </div>
      <div class="m-button" @click="removeRow">
        <i class="fa fa-minus-circle"></i> Usuń wiersz
      </div>
    </div>
    <div class="tableNameSec">
      Nazwa arkusza:
      <input placeholder="Wprowadź nazwę" v-model="tableName">
      <button class="b-ok" @click="addName">OK</button>
    </div>

    <div class="m-footer">
      <hr>Wykonała:
      <a
        href="https://www.linkedin.com/in/monika-wladysiak"
        target="_blank"
      >Monika Władysiak</a>
    </div>
  </div>
</template>

<script>
export default {
  name: "App",
  data() {
    return {
      tableName: "",
      tableLabel: "Przykładowa nazwa arkusza",
      validation: "",
      rows: [[1, 2, 3, 5], [8, 0, 1, 2], [3, 5, 8, 0], [1, 2, 3, 5]],
      columns: 4,
      newRow: [],
      sum: [13, 9, 15, 12],
      colSum: 0,
      rowSum: 0,
      rowAvg: [2.75, 2.75, 4, 2.75],
      isEditable: false,
      regDisplay: false
    };
  },
  methods: {
    prepreNewState() {
      this.clearMsgBox();
      this.countSum();
      this.countAvrg();
    },
    clearMsgBox() {
      this.validation = "";
    },
    addName() {
      this.prepreNewState();
      this.tableLabel = this.tableName;
    },
    restartCreating() {
      this.columns = 4;
      this.tableLabel = "Przykładowa nazwa arkusza";
      this.rows = [[1, 2, 3, 5], [8, 0, 1, 2], [3, 5, 8, 0], [1, 2, 3, 5]];
      this.prepreNewState();
    },
    addRow() {
      for (var i = 0; i < this.columns; i++) {
        this.newRow.push(1);
      }
      this.rows.push(this.newRow);
      this.prepreNewState();
    },
    addColumn() {
      this.columns += 1;
      for (var i = 0; i < this.rows.length; i++) {
        this.rows[i].push(1);
      }
      this.prepreNewState();
    },
    removeRow() {
      if (this.rows.length < 1) {
        this.validation = "Brak wierszy do usunięcia.";
      } else {
        this.rows.splice(this.rows.length - 1, 1);
        this.prepreNewState();
      }
    },
    removeColumn() {
      if (this.columns < 1) {
        this.validation = "Brak kolumn do usunięcia.";
      } else {
        this.columns -= 1;
        for (var i = 0; i < this.rows.length; i++) {
          this.rows[i].splice(this.rows[i].length - 1, 1);
        }
        this.prepreNewState();
      }
    },
    countSum() {
      this.sum = [];
      for (var i = 0; i < this.columns; i++) {
        this.colSum = 0;
        for (var a = 0; a < this.rows.length; a++) {
          if (this.rows[a][i] !== 0) {
            this.colSum = this.colSum + parseFloat(this.rows[a][i]);
          }
          //this.colSum = this.colSum + parseFloat(this.rows[a][i]);
        }
        this.sum.push(this.colSum);
      }
    },
    countAvrg() {
      this.rowAvg = [];
      for (var i = 0; i < this.rows.length; i++) {
        this.rowSum = 0;
        for (var a = 0; a < this.rows[i].length; a++) {
          var denominator = this.rows[i].length - 1;

          if (this.rows[i][a] !== 0) {
            this.rowSum += parseFloat(this.rows[i][a]);
          } else {
            denominator -= 1;
          }
        }
        this.rowSum = Math.round((this.rowSum / denominator) * 100) / 100;
        this.rowAvg.push(this.rowSum);
      }
    },
    makeEditable() {
      this.isEditable = true;
      this.regDisplay = true;
    },
    saveChanges() {
      var rows = document.getElementsByClassName("row-input");
      var errors = false;

      for (var i = 0; i < rows.length; i++) {
        if (
          isNaN(rows[i].value) ||
          Number.isInteger(parseFloat(rows[i].value)) === false
        ) {
          this.validation =
            "Nie wszystkie wprowadzone w tabeli wartości są poprawne. Wprowadź wyłącznie cyfry.";

          errors = true;
        }
      }
      if (errors === false) {
        this.isEditable = false;
        this.regDisplay = false;
        this.prepreNewState();
      }
    }
  }
};
</script>

<style>
html {
  font-family: "Source Sans Pro", "Helvetica Neue", sans-serif;
  font-weight: 300;
  background-image: url("https://jankochanowski.github.io/vuejs-recruitment/public/assets/bgr7.jpg");
  background-position: center;
  background-repeat: no-repeat;
  background-size: cover;
  z-index: 0;
  min-height: 100%;
  padding-top: 50px;
  padding-bottom: 30px;
  padding-left: 30px;
  padding-right: 30px;
  margin: 0;
  overflow-x: hidden;
  background-repeat: no-repeat;
}
.wrapper-div {
  background-color: rgba(255, 255, 255, 0.92);
  padding-top: 30px;
  padding-bottom: 30px;
  padding-left: 50px;
  padding-right: 50px;
  margin: auto;
  max-width: 800px;
  border-radius: 20px;
  box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.3), 0 8px 22px 0 rgba(0, 0, 0, 0.19);
}
.logo-text {
  font-weight: 300;
  font-size: 3em;
}
.logoVueJs {
  max-width: 70px;
}
.mHeader {
  text-align: center;
  padding-bottom: 20px;
}
.header-label {
  font-size: 1.4em;
}
.m-highlight {
  font-weight: 400;
  color: #35495E;
}
.mHeader > p {
  text-align: justify;
  font-size: 0.9em;
}
.tableWrapper {
  overflow-x: scroll;
  width: 100%;
}

table {
  margin: 10px 10px 0 10px;
  font-size: 1.1em;
  max-width: 600px;
  overflow-x: auto;
  border-collapse: collapse;
}

table th {
  font-weight: 300;
  text-align: left;
  background: #35495E;
  color: #FFF;
  padding: 8px;
  min-width: 30px;
}

table td {
  text-align: left;
  padding: 8px;
  /*border-right: 1px solid white;*/
  background-color: #F0F0F0;
}

table tbody tr:nth-child(2n) td {
  background-color: #E8E8E8;
}
.sum-col {
  font-weight: 500;
  background-color: rgba(255, 255, 255, 0) !important;
}

.button-section {
  display: flex;
  flex-wrap: wrap;
}

.b-save {
  display: none;
  background-color: red !important;
  border: none !important;
  color: white !important;
}

.m-button {
  padding: 10px;
  border-radius: 18px;
  background-color: white;
  color: #35495E;
  margin-top: 6px;
  margin-right: 6px;
  margin-left: 6px;
  text-align: center;
  border: solid 1px #304455;
}

.m-spec {
  background-color: #35495E !important;
  border: solid 1px #35495E !important;
  color: white !important;
}

.m-button:hover {
  background-color: white !important;
  color: #4fc08d !important;
  border: solid 1px #4fc08d !important;
  cursor: pointer;
}
.m-actions {
  margin-top: 40px;
  color: #35495E;
  padding-left: 10px;
  font-weight: 400;
}
.b-ok {
  background-color: #4fc08d;
  border-radius: 6px;
  width: 30px;
  height: 20px;
  text-align: center;
  margin-left: 10px;
  color: white;
  border: none;
  padding-left: 6px;
  padding-right: 6px;
  cursor: pointer;
}
.m-footer {
  font-size: 0.8em;
  text-align: center;
  margin-top: 40px;
}
.m-footer > a {
  color: #35495E;
  text-decoration: none;
}

.m-footer > hr {
  max-width: 150px;
  border: solid 0.5px #35495E;
}
.tableNameSec {
  font-size: 0.9em;
  padding-top: 10px;
}
.tableNameSec > input {
  padding: 8px;
  font-size: 1em;
  border: 1px solid #ccc;
  border-radius: 4px;
}

.m-validation {
  font-size: 0.8em;
  color: red;
}
.row-input {
  width: 1.5em;
  height: 1.5em;
  text-align: center;
  font-size: 1em;
  font-weight: 300;
  display: none;
}
.iseditable {
  display: block;
}
.regdisplay {
  display: none;
}
</style>

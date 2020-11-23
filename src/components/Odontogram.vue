<template>
  <div>
    <h3
      class="rounded text-white"
      style="background-color: #d0cd9a; padding: 6px;"
    >
      Odontograma Demo
    </h3>
    <div class="row p-3">
      <div class="col-lg-8 col-md-7 col-sm-12">
        <canvas
          class="img-responsive border border-primary rounded"
          id="canvas"
          width="660"
          height="700"
        ></canvas>
      </div>
      <div
        class="col-lg-4 col-md-5 col-sm-12 rounded"
        style="background-color: #d0cd9a; padding: 6px;"
      >
        <div class="form-group">
          <b>Tipo Paciente</b>
          <select
            v-model="data.patienttype"
            class="form-control form-control-sm"
          >
            <option selected disabled value="">--Seleccione Paciente--</option>
            <option value="adult">Adulto</option>
            <option value="childish">Infantil</option>
          </select>
          <div class="help-block" v-if="$v.data.patienttype.$error">
            <small class="text-danger" v-if="!$v.data.patienttype.required"
              >El campo es requerido</small
            >
          </div>
        </div>
        <div class="form-group">
          <b>Posición</b>
          <select v-model="data.position" class="form-control form-control-sm">
            <option selected disabled value="">--Seleccione Posición--</option>
            <option value="sup">Superior</option>
            <option value="inf">Inferior</option>
          </select>
          <div class="help-block" v-if="$v.data.position.$error">
            <small class="text-danger" v-if="!$v.data.position.required"
              >El campo es requerido</small
            >
          </div>
        </div>
        <div class="form-group">
          <b>Pieza</b>
          <select v-model="data.tooth" class="form-control form-control-sm">
            <option selected disabled>--Seleccione Pieza--</option>
            <option v-for="x in teeth" :key="x.name" :value="x.name">{{
              x.label
            }}</option>
          </select>
          <div class="help-block" v-if="$v.data.tooth.$error">
            <small class="text-danger" v-if="!$v.data.tooth.required"
              >El campo es requerido</small
            >
          </div>
        </div>
        <div class="form-group">
          <h6><b>Cuadrante a Trabajar</b></h6>
          <div class="form-check form-check-inline">
            <input
              v-model="data.quadrant.v"
              class="form-check-input"
              type="checkbox"
              name="checkboxCuadrante"
              id="checkboxCuadranteV"
            />
            <label class="form-check-label" for="checkboxCuadranteV">
              V
            </label>
          </div>
          <div class="form-check form-check-inline">
            <input
              v-model="data.quadrant.d"
              class="form-check-input"
              type="checkbox"
              name="checkboxCuadrante"
              id="checkboxCuadranteD"
            />
            <label class="form-check-label" for="checkboxCuadranteD">
              D
            </label>
          </div>
          <div class="form-check form-check-inline">
            <input
              v-model="data.quadrant.o"
              class="form-check-input"
              type="checkbox"
              name="checkboxCuadrante"
              id="checkboxCuadranteO"
            />
            <label class="form-check-label" for="checkboxCuadranteO">
              O
            </label>
          </div>
          <div class="form-check form-check-inline">
            <input
              v-model="data.quadrant.m"
              class="form-check-input"
              type="checkbox"
              name="checkboxCuadrante"
              id="checkboxCuadranteM"
            />
            <label class="form-check-label" for="checkboxCuadranteM">
              M
            </label>
          </div>
          <div class="form-check form-check-inline">
            <input
              v-model="data.quadrant.p"
              class="form-check-input"
              type="checkbox"
              name="checkboxCuadrante"
              id="checkboxCuadranteP"
            />
            <label class="form-check-label" for="checkboxCuadranteP">
              P
            </label>
          </div>
        </div>
        <div class="form-group">
          <b>Diagnóstico</b>
          <select
            v-model="data.diagnostic"
            class="form-control form-control-sm"
          >
            <option selected disabled>--Seleccione Diagnóstico--</option>
            <option v-for="x in diagnostic" :key="x.name" :value="x.value">
              {{ x.name }}
            </option>
          </select>
        </div>
        <div class="form-group">
          <div class="form-check form-check-inline">
            <input
              v-model="data.treat"
              class="form-check-input"
              type="checkbox"
              name="checkboxTratar"
              id="checkboxTratar"
            />
            <label class="form-check-label" for="checkboxTratar">
              Marcar para tratar
            </label>
          </div>
        </div>
        <div class="form-group">
          <b>Detalle</b>
          <textarea
            v-model="data.detail"
            class="form-control form-control-sm border-patient-controls"
          />
        </div>
        <div class="form-group">
          <button class="btn btn-primary btn-sm" @click="add">Agregar</button>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import { required } from "vuelidate/lib/validators";
import teethdata from "@/assets/data/teethdata.json";
import diagnostic from "@/assets/data/diagnostic.json";

const defaultColor = "#000000";

export default {
  name: "odontogram",
  data: () => ({
    data: {
      position: "",
      patienttype: "",
      tooth: "",
      diagnostic: "",
      detail: "",
      treat: false,
      quadrant: {
        v: false,
        d: false,
        o: false,
        m: false,
        p: false
      }
    },
    ctx: {},
    diagnostic: diagnostic,
    odon: teethdata,
    odonto: {
      datalist: []
    }
  }),
  validations: {
    data: {
      position: { required },
      patienttype: { required },
      tooth: { required },
      diagnostic: { required }
    }
  },
  methods: {
    clean: function() {
      this.data = {
        position: "",
        patienttype: "",
        tooth: "",
        diagnostic: "",
        detail: "",
        treat: false,
        quadrant: {
          v: false,
          d: false,
          o: false,
          m: false,
          p: false
        }
      };
      this.ctx.fillStyle = defaultColor;
    },
    init: function() {
      var self = this;
      var canvas = document.getElementById("canvas");
      self.ctx = canvas.getContext("2d");
      // clean context.
      self.ctx.setTransform(1, 0, 0, 1, 0, 0);
      self.ctx.clearRect(0, 0, canvas.width, canvas.height);
      self.ctx.restore();

      self.ctx.textAlign = "center";
      self.ctx.fillStyle = defaultColor;
      self.ctx.font = "13px Arial";
      //Horizontal Line.
      self.drawLine(0, 340, 660, 340, "#C0C0C0");
      //Vertical Line.
      self.drawLine(330, 0, 330, 700, "#C0C0C0");

      for (let l of self.odon.settings.lines) {
        let x = l.initial;
        for (let i = 0; i < l.total; i++) {
          self.drawLine(x, l.x2, x, l.y2, l.color);
          x += l.length;
        }
      }

      for (let info of self.odon.values) {
        self.addImage(info);
      }

      canvas.addEventListener(
        "mousedown",
        function(event) {
          const bounds = canvas.getBoundingClientRect();
          self.captureEvent(event, bounds);
        },
        false
      );
    },
    drawLine: function(x, y, x2, y2, color) {
      this.ctx.beginPath();
      this.ctx.moveTo(x, y);
      this.ctx.strokeStyle = color;
      this.ctx.lineTo(x2, y2);
      this.ctx.stroke();
    },
    addImage: function(info) {
      var self = this;
      let image = new Image();
      image.src = require(`@/assets/teeth/dentadura-${info.prefix}-${info.name}.png`);

      image.onload = function() {
        self.ctx.fillText(info.value, info.textposition.x, info.textposition.y);
        self.ctx.drawImage(this, info.imageposition.x, info.imageposition.y);

        for (let c of info.cuadrantes) {
          self.drawRect(c.x, c.y, 0.4);
        }
      };
    },
    drawRect: function(x, y, ga) {
      let size = this.odon.settings.recSize;
      this.ctx.beginPath();
      this.ctx.globalAlpha = ga;

      this.ctx.fillRect(x, y, size, size);

      this.ctx.globalAlpha = 1;
      this.ctx.restore();
    },
    add: function() {
      var self = this;
      self.$v.$touch();

      if (!self.$v.$invalid) {
        self.$v.$reset();
        const d = self.data;
        const piece = self.odon.values.filter(
          item =>
            item.name == d.tooth &&
            item.prefix == d.position &&
            item.type == d.patienttype
        );

        if (piece.length >= 1) {
          let arr = [];

          if (d.quadrant.o) arr.push("o");

          if (d.quadrant.v) arr.push("v");

          if (d.quadrant.d) arr.push("d");

          if (d.quadrant.m) arr.push("m");

          if (d.quadrant.p) arr.push("p");

          const diag = self.diagnostic.filter(x => x.value == d.diagnostic);
          self.ctx.fillStyle = defaultColor;
          if (diag.length >= 1) {
            self.ctx.fillStyle = diag[0].color;
          }
          for (let p of arr) {
            let q = piece[0].cuadrantes.filter(i => i.name == p)[0];
            if (q) {
              self.drawRect(q.x, q.y, 1);
            }
          }
          self.odonto.datalist.push(self.data);
          this.clean();
        }
      }
    }
  },
  computed: {
    teeth: function() {
      let result = [];
      if (this.data.position == "" && this.data.patienttype == "") {
        result = this.odon.values.map(item => {
          return {
            name: item.name,
            label: item.value
          };
        });
        return result;
      }

      if (this.data.position != "" && this.data.patienttype != "") {
        result = this.odon.values
          .filter(
            x =>
              x.prefix == this.data.position && x.type == this.data.patienttype
          )
          .map(item => {
            return {
              name: item.name,
              label: item.value
            };
          });
        return result;
      }

      if (this.data.position != "") {
        result = this.odon.values
          .filter(x => x.prefix == this.data.position)
          .map(item => {
            return {
              name: item.name,
              label: item.value
            };
          });
        return result;
      }

      if (this.data.patienttype != "") {
        result = this.odon.values
          .filter(x => x.type == this.data.patienttype)
          .map(item => {
            return {
              name: item.name,
              label: item.value
            };
          });
        return result;
      }
      return result;
    }
  },
  mounted() {
    this.init();
  }
};
</script>

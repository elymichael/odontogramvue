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
          <select class="form-control form-control-sm">
            <option selected disabled>--Seleccione Paciente--</option>
            <option>Adulto</option>
            <option>Infantil</option>
          </select>
        </div>
        <div class="form-group">
          <b>Posición</b>
          <select class="form-control form-control-sm">
            <option selected disabled>--Seleccione Posición--</option>
            <option>Superior</option>
            <option>Inferior</option>
          </select>
        </div>
        <div class="form-group">
          <b>Pieza</b>
          <select class="form-control form-control-sm">
            <option selected disabled>--Seleccione Pieza--</option>
          </select>
        </div>
        <div class="form-group">
          <h6><b>Cuadrante a Trabajar</b></h6>
          <div class="form-check form-check-inline">
            <input
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
          <select class="form-control form-control-sm">
            <option selected disabled>--Seleccione Diagnóstico--</option>
            <option v-for="x in diagnostic" :key="x.name">
              {{ x.name }}
            </option>
          </select>
        </div>
        <div class="form-group">
          <div class="form-check form-check-inline">
            <input
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
            class="form-control form-control-sm border-patient-controls"
          />
        </div>
        <div class="form-group">
          <button class="btn btn-primary btn-sm">Agregar</button>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import teethdata from "@/assets/data/teethdata.json";
import diagnostic from "@/assets/data/diagnostic.json";

export default {
  name: "odontogram",
  data: () => ({
    ctx: {},
    diagnostic: diagnostic,
    odon: teethdata
  }),
  methods: {
    init: function() {
      var self = this;
      var canvas = document.getElementById("canvas");
      self.ctx = canvas.getContext("2d");
      // clean context.
      self.ctx.setTransform(1, 0, 0, 1, 0, 0);
      self.ctx.clearRect(0, 0, canvas.width, canvas.height);
      self.ctx.restore();

      self.ctx.textAlign = "center";
      self.ctx.fillStyle = "#000000";
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
          self.drawRect(c.x, c.y);
        }
      };
    },
    drawRect: function(x, y) {
      let size = this.odon.settings.recSize;
      this.ctx.beginPath();
      this.ctx.globalAlpha = 0.4;

      this.ctx.fillRect(x, y, size, size);

      this.ctx.globalAlpha = 1;
      this.ctx.restore();
    }
  },
  mounted() {
    this.init();
  }
};
</script>

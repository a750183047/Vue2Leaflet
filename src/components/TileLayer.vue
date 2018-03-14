<template>
</template>

<script>
import L from 'leaflet';
import propsBinder from '../utils/propsBinder.js';
import eventsBinder from '../utils/eventsBinder.js';

const events = [
  'loading',
  'tileunload',
  'tileloadstart',
  'tileerror',
  'tileload',
  'load',
  'add',
  'remove',
  'popupopen',
  'popupclose',
  'tooltipopen',
  'tooltipclose'
];

const props = {
  url: String,
  attribution: {
    type: String,
    custom: true
  },
  detectRetina: {
    type: Boolean,
    custom: false,
    default: false
  },
  token: {
    type: String,
    custom: true
  },
  opacity: {
    type: Number,
    custom: false,
    default: 1.0
  },
  zIndex: {
    type: Number,
    default: 1
  },
  bounds: {
    custom: true,
    default: undefined,
  },
  options: {
    type: Object,
    default: function() {
      return {};
    }
  },
  tileLayerClass: {
  	type: Function,
	default: L.tileLayer
  }
};

export default {
  props: props,
  mounted() {
    const options = this.options;
    const otherPropertytoInitialize = [ "attribution", "token", "detectRetina", "opacity", "zIndex","bounds" ];
    for (var i = 0; i < otherPropertytoInitialize.length; i++) {
      const propName = otherPropertytoInitialize[i];
      if(this[propName] !== undefined) {
        options[propName] = this[propName];
      }
    }
	this.options.bounds = L.LatLngBounds(L.latLng(-49.875, 34.25), L.latLng(-206, 221))
    this.mapObject = this.tileLayerClass(this.url, options);
    eventsBinder(this, this.mapObject, events);
    propsBinder(this, this.mapObject, props);
  },
  methods: {
    deferredMountedTo(parent) {
      this.mapObject.addTo(parent);
      this.attributionControl = parent.attributionControl;
      for (var i = 0; i < this.$children.length; i++) {
        if (typeof this.$children[i].deferredMountedTo === "function") {
          this.$children[i].deferredMountedTo(this.mapObject);
        }
      }
    },
    setAttribution(val, old) {
      this.attributionControl.removeAttribution(old);
      this.attributionControl.addAttribution(val);
    },
    setToken(val) {
      this.options.token = val;
    }
  },
  beforeDestroy() {
    this.$parent.mapObject.removeLayer(this.mapObject);
  }
};
</script>

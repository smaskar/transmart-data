include ../../makefile.inc

ifndef KETTLE_JOBS
$(error KETTLE_JOBS is not set)
endif
ifndef KITCHEN
$(error KITCHEN is not set)
endif

CLINICAL_PARAMS_FILES := $(wildcard ../common/*/clinical.params)
ANNOTATION_PARAMS_FILES := $(wildcard ../common/*/annotation.params)
REF_ANNOTATION_PARAMS_FILES := $(wildcard ../common/*/ref_annotation.params)
EXPRESSION_PARAMS_FILES := $(wildcard ../common/*/expression.params)
ANALYSIS_PARAMS_FILES := $(wildcard ../common/*/analysis.params)

LOAD_CLINICAL_TARGETS := $(patsubst ../common/%/clinical.params,load_clinical_%, $(CLINICAL_PARAMS_FILES))
LOAD_ANNOTATION_TARGETS := $(patsubst ../common/%/annotation.params,load_annotation_%, $(ANNOTATION_PARAMS_FILES))
LOAD_REF_ANNOTATION_TARGETS := $(patsubst ../common/%/ref_annotation.params,load_ref_annotation_%, $(REF_ANNOTATION_PARAMS_FILES))
LOAD_EXPRESSION_PARAMS_TARGETS := $(patsubst ../common/%/expression.params,load_expression_%, $(EXPRESSION_PARAMS_FILES))
LOAD_ANALYSIS_PARAMS_TARGETS := $(patsubst ../common/%/analysis.params,load_analysis_%, $(ANALYSIS_PARAMS_FILES))

KETTLE_TARGETS := $(LOAD_CLINICAL_TARGETS) $(LOAD_EXPRESSION_PARAMS_TARGETS) $(LOAD_ANALYSIS_PARAMS_TARGETS)

$(KETTLE_TARGETS): write_kettle_properties
#these ones are here just for autocompletion:
$(LOAD_ANNOTATION_TARGETS):
$(LOAD_REF_ANNOTATION_TARGETS):

write_kettle_properties: gen_kettle_properties.php
	php -d variables_order=E $< > kettle-home/kettle.properties

load_clinical_%: ../common/%/clinical.params ../common/%/clinical
	STUDY_ID=$* DATA_LOCATION="$(realpath ../common/$*/clinical)" \
			KETTLE_HOME="$(realpath kettle-home)" \
			./load_clinical.sh $<

load_annotation_%: ../common/%/annotation.params ../common/%/annotation
	DATA_LOCATION="$(realpath ../common/$*/annotation)" \
		PSQL_COMMAND="$(PSQL_COMMAND)" \
		./load_annotation.sh $<

load_ref_annotation_%: ../common/%/ref_annotation.params
	$(MAKE) load_annotation_`./load_ref_annotation.sh $<`

load_expression_%: ../common/%/expression.params ../common/%/expression
	STUDY_ID=$* DATA_LOCATION="$(realpath ../common/$*/expression)" \
			KETTLE_HOME="$(realpath kettle-home)" \
			./load_expression.sh $<

load_analysis_%: ../common/%/analysis.params ../common/%/analysis
	STUDY_ID=$* DATA_LOCATION="$(realpath ../common/$*/analysis)" \
			KETTLE_HOME="$(realpath kettle-home)" \
			./load_analysis.sh $<

../common/%/clinical: ../common/Makefile
	$(MAKE) -C ../common $*/clinical

../common/%/annotation: ../common/Makefile
	$(MAKE) -C ../common $*/annotation

../common/%/expression: ../common/Makefile
	$(MAKE) -C ../common $*/expression

../common/%/analysis: ../common/Makefile
	$(MAKE) -C ../common $*/analysis

clean:
	rm -f logs/*.log kettle-home/kettle.properties

.PHONY: write_kettle_properties clean
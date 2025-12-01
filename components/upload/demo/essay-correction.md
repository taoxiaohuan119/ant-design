---
order: 20
title:
  zh-CN: 拍照批改作文
  en-US: Photo Essay Correction
---

## zh-CN

手机拍照批改作文交互流程：第一步拍摄写作要求（可拍多张），第二步在弹窗中修改参数，第三步拍摄作文。

## en-US

Mobile photo essay correction workflow: Step 1 - Take photos of writing requirements (multiple photos allowed), Step 2 - Modify parameters in modal, Step 3 - Take photos of essays.

```jsx
import { Upload, Modal, Form, Input, Select, Button, Steps, Icon } from 'antd';

const { Option } = Select;
const { Step } = Steps;

function getBase64(file) {
  return new Promise((resolve, reject) => {
    const reader = new FileReader();
    reader.readAsDataURL(file);
    reader.onload = () => resolve(reader.result);
    reader.onerror = error => reject(error);
  });
}

// 表单弹窗组件
const ParameterFormModal = Form.create({ name: 'parameter_form' })(
  class extends React.Component {
    render() {
      const { visible, onCancel, onNext, form, initialValues } = this.props;
      const { getFieldDecorator } = form;
      return (
        <Modal
          visible={visible}
          title="设置批改参数"
          okText="下一步"
          cancelText="取消"
          onCancel={onCancel}
          onOk={() => {
            form.validateFields((err, values) => {
              if (!err) {
                onNext(values);
              }
            });
          }}
          width={600}
        >
          <Form layout="vertical">
            <Form.Item label="作文类型">
              {getFieldDecorator('essayType', {
                initialValue: initialValues?.essayType || 'narrative',
                rules: [{ required: true, message: '请选择作文类型!' }],
              })(
                <Select placeholder="请选择作文类型">
                  <Option value="narrative">记叙文</Option>
                  <Option value="argumentative">议论文</Option>
                  <Option value="expository">说明文</Option>
                  <Option value="descriptive">描写文</Option>
                </Select>,
              )}
            </Form.Item>
            <Form.Item label="字数要求">
              {getFieldDecorator('wordCount', {
                initialValue: initialValues?.wordCount || '500',
                rules: [{ required: true, message: '请输入字数要求!' }],
              })(
                <Select placeholder="请选择字数要求">
                  <Option value="300">300字</Option>
                  <Option value="400">400字</Option>
                  <Option value="500">500字</Option>
                  <Option value="600">600字</Option>
                  <Option value="800">800字</Option>
                  <Option value="1000">1000字</Option>
                </Select>,
              )}
            </Form.Item>
            <Form.Item label="评分标准">
              {getFieldDecorator('gradingCriteria', {
                initialValue: initialValues?.gradingCriteria || 'standard',
                rules: [{ required: true, message: '请选择评分标准!' }],
              })(
                <Select placeholder="请选择评分标准">
                  <Option value="standard">标准评分</Option>
                  <Option value="strict">严格评分</Option>
                  <Option value="lenient">宽松评分</Option>
                </Select>,
              )}
            </Form.Item>
            <Form.Item label="备注">
              {getFieldDecorator('notes', {
                initialValue: initialValues?.notes || '',
              })(<Input.TextArea rows={4} placeholder="请输入备注信息（可选）" />)}
            </Form.Item>
          </Form>
        </Modal>
      );
    }
  },
);

class EssayCorrection extends React.Component {
  state = {
    // 步骤：1-拍摄要求, 2-参数设置, 3-拍摄作文
    currentStep: 1,
    // 写作要求照片列表
    requirementFileList: [],
    // 参数设置弹窗显示状态
    parameterModalVisible: false,
    // 表单参数
    formParameters: {
      essayType: 'narrative',
      wordCount: '500',
      gradingCriteria: 'standard',
      notes: '',
    },
    // 作文照片列表
    essayFileList: [],
    // 图片预览
    previewVisible: false,
    previewImage: '',
  };

  // 处理写作要求照片变化
  handleRequirementChange = ({ fileList }) => {
    this.setState({ requirementFileList: fileList });
  };

  // 处理写作要求照片预览
  handleRequirementPreview = async file => {
    if (!file.url && !file.preview) {
      file.preview = await getBase64(file.originFileObj);
    }
    this.setState({
      previewImage: file.url || file.preview,
      previewVisible: true,
    });
  };

  // 第一步：点击下一步，显示参数设置弹窗
  handleRequirementNext = () => {
    const { requirementFileList } = this.state;
    if (requirementFileList.length === 0) {
      Modal.warning({
        title: '提示',
        content: '请至少拍摄一张写作要求照片！',
      });
      return;
    }
    this.setState({
      parameterModalVisible: true,
    });
  };

  // 参数设置弹窗取消
  handleParameterCancel = () => {
    this.setState({
      parameterModalVisible: false,
    });
  };

  // 参数设置弹窗下一步
  handleParameterNext = values => {
    this.setState({
      formParameters: values,
      parameterModalVisible: false,
      currentStep: 3, // 进入第三步：拍摄作文
    });
  };

  // 处理作文照片变化
  handleEssayChange = ({ fileList }) => {
    this.setState({ essayFileList: fileList });
  };

  // 处理作文照片预览
  handleEssayPreview = async file => {
    if (!file.url && !file.preview) {
      file.preview = await getBase64(file.originFileObj);
    }
    this.setState({
      previewImage: file.url || file.preview,
      previewVisible: true,
    });
  };

  // 关闭预览
  handlePreviewCancel = () => {
    this.setState({ previewVisible: false });
  };

  // 保存表单引用
  saveFormRef = formRef => {
    this.formRef = formRef;
  };

  render() {
    const {
      currentStep,
      requirementFileList,
      parameterModalVisible,
      formParameters,
      essayFileList,
      previewVisible,
      previewImage,
    } = this.state;

    const uploadButton = (
      <div>
        <Icon type="camera" style={{ fontSize: 32, color: '#999' }} />
        <div style={{ marginTop: 8, color: '#666' }}>拍照</div>
      </div>
    );

    return (
      <div style={{ padding: '20px', maxWidth: '800px', margin: '0 auto' }}>
        <Steps current={currentStep - 1} style={{ marginBottom: '40px' }}>
          <Step title="拍摄写作要求" />
          <Step title="设置参数" />
          <Step title="拍摄作文" />
        </Steps>

        {/* 第一步：拍摄写作要求 */}
        {currentStep === 1 && (
          <div>
            <h3 style={{ marginBottom: '16px' }}>第一步：拍摄写作要求</h3>
            <p style={{ marginBottom: '16px', color: '#666' }}>
              请拍摄写作要求的照片，可以拍摄一张或多张
            </p>
            <Upload
              action="https://www.mocky.io/v2/5cc8019d300000980a055e76"
              listType="picture-card"
              fileList={requirementFileList}
              onPreview={this.handleRequirementPreview}
              onChange={this.handleRequirementChange}
              accept="image/*"
              capture="environment"
            >
              {requirementFileList.length >= 10 ? null : uploadButton}
            </Upload>
            <div style={{ marginTop: '24px', textAlign: 'right' }}>
              <Button type="primary" size="large" onClick={this.handleRequirementNext}>
                下一步
              </Button>
            </div>
          </div>
        )}

        {/* 第二步：参数设置弹窗 */}
        <ParameterFormModal
          wrappedComponentRef={this.saveFormRef}
          visible={parameterModalVisible}
          onCancel={this.handleParameterCancel}
          onNext={this.handleParameterNext}
          initialValues={formParameters}
        />

        {/* 第三步：拍摄作文 */}
        {currentStep === 3 && (
          <div>
            <h3 style={{ marginBottom: '16px' }}>第三步：拍摄作文</h3>
            <p style={{ marginBottom: '16px', color: '#666' }}>
              请拍摄学生作文的照片，可以拍摄一张或多张
            </p>
            <Upload
              action="https://www.mocky.io/v2/5cc8019d300000980a055e76"
              listType="picture-card"
              fileList={essayFileList}
              onPreview={this.handleEssayPreview}
              onChange={this.handleEssayChange}
              accept="image/*"
              capture="environment"
            >
              {essayFileList.length >= 10 ? null : uploadButton}
            </Upload>
            <div style={{ marginTop: '24px' }}>
              <div style={{ marginBottom: '16px', padding: '12px', background: '#f5f5f5', borderRadius: '4px' }}>
                <h4 style={{ marginBottom: '8px' }}>当前设置参数：</h4>
                <p>作文类型：{formParameters.essayType === 'narrative' ? '记叙文' : formParameters.essayType === 'argumentative' ? '议论文' : formParameters.essayType === 'expository' ? '说明文' : '描写文'}</p>
                <p>字数要求：{formParameters.wordCount}字</p>
                <p>评分标准：{formParameters.gradingCriteria === 'standard' ? '标准评分' : formParameters.gradingCriteria === 'strict' ? '严格评分' : '宽松评分'}</p>
                {formParameters.notes && <p>备注：{formParameters.notes}</p>}
              </div>
              <div style={{ textAlign: 'right' }}>
                <Button
                  style={{ marginRight: '8px' }}
                  onClick={() => {
                    this.setState({
                      parameterModalVisible: true,
                    });
                  }}
                >
                  修改参数
                </Button>
                <Button
                  style={{ marginRight: '8px' }}
                  onClick={() => {
                    this.setState({
                      currentStep: 1,
                      essayFileList: [],
                    });
                  }}
                >
                  返回重拍要求
                </Button>
                <Button
                  type="primary"
                  size="large"
                  onClick={() => {
                    if (essayFileList.length === 0) {
                      Modal.warning({
                        title: '提示',
                        content: '请至少拍摄一张作文照片！',
                      });
                      return;
                    }
                    Modal.success({
                      title: '完成',
                      content: '所有照片已拍摄完成，可以开始批改了！',
                    });
                  }}
                >
                  完成
                </Button>
              </div>
            </div>
          </div>
        )}

        {/* 图片预览 */}
        <Modal visible={previewVisible} footer={null} onCancel={this.handlePreviewCancel}>
          <img alt="preview" style={{ width: '100%' }} src={previewImage} />
        </Modal>
      </div>
    );
  }
}

ReactDOM.render(<EssayCorrection />, mountNode);
```

```css
.ant-upload-select-picture-card i {
  font-size: 32px;
  color: #999;
}

.ant-upload-select-picture-card .ant-upload-text {
  margin-top: 8px;
  color: #666;
}
```

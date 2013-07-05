## Introduction

This library provides simple C++ wrappers around some OpenGL functionality. It was modified from an [OpenGL 4 library](https://github.com/evanw/gl4) created for the [Advanced GPU Programming](http://cs.brown.edu/courses/cs195v/) course at Brown University.

## Example

    #ifdef __APPLE_CC__
    #include <GLUT/glut.h>
    #else
    #include <GL/glut.h>
    #endif
    #include "gl.h"

    Shader shader;
    Buffer<vec2> quad;
    VAO layout;

    void setup() {
        shader.vertexShader(glsl(
            attribute vec2 vertex;
            varying vec2 coord;
            void main() {
                coord = vertex * 0.5 + 0.5;
                gl_Position = vec4(vertex, 0.0, 1.0);
            }
        )).fragmentShader(glsl(
            varying vec2 coord;
            void main() {
                gl_FragColor = vec4(coord, 0.0, 1.0);
            }
        )).link();

        quad << vec2(-1, -1) << vec2(1, -1) << vec2(-1, 1) << vec2(1, 1);
        quad.upload();
        layout.create(shader, quad).attribute<float>("vertex", 2).check();
    }

    void draw() {
        glClear(GL_COLOR_BUFFER_BIT);
        shader.use();
        layout.draw(GL_TRIANGLE_STRIP);
        shader.unuse();
        glutSwapBuffers();
    }

    int main(int argc, char *argv[]) {
        glutInit(&argc, argv);
        glutInitDisplayMode(GLUT_DOUBLE | GLUT_RGB);
        glutCreateWindow("Example");
        glutReshapeWindow(800, 600);
        glutDisplayFunc(draw);
        setup();
        glutMainLoop();
        return 0;
    }
